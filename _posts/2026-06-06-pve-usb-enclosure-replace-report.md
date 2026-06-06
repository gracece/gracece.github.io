---
layout: post
title: "  PVE 飞牛外接硬盘盒更换 + 存储池恢复备忘"
description: ""
category: 生活
tags: []
---

## 个人总结

- 旧硬盘盒（Ugreen/JMicron JMS578）物理损坏，更换为 UNITEK/ASMedia ASM1153E
- 新芯片 VID:PID 变了，需要更新 UAS 禁用规则
- 数据全程零丢失

---

# PVE 外接硬盘盒更换与存储池恢复技术报告
**报告版本：** V1.0
**报告时间：** 2026年6月6日
**故障环境：** Proxmox VE 8.x（内核 6.8.8-1-pve）+ 飞牛 FNOS 虚拟机（VM 103）
**事件背景：** 上次修复（2026-04-27）后，外接硬盘盒物理损坏，更换新硬盘盒
**数据状态：** 全程数据零丢失

---

## 一、环境详情

| 层级 | 详细配置 |
|------|----------|
| 宿主机 | Intel N100 迷你主机，Proxmox VE 8.x（内核 6.8.8-1-pve） |
| 虚拟机 | VM ID 103，运行飞牛 FNOS |
| 存储设备 | 方向（Fanxiang）S100Pro 1TB 2.5英寸机械硬盘 |
| 旧硬盘盒 | Ugreen 绿联，JMicron JMS578 桥接芯片，VID:PID=`152d:0578` |
| 新硬盘盒 | UNITEK，ASMedia ASM1153E 桥接芯片，VID:PID=`174c:55aa` |
| 存储架构 | FNOS Basic 单盘存储池，mdadm RAID1 + LVM + ext4，挂载路径 `/vol3` |

---

## 二、故障现象

1. 旧硬盘盒物理损坏，硬盘无法被 PVE 识别
2. 购买新硬盘盒（UNITEK/ASMedia）后，首次插入 PVE 时硬盘未被盒子识别（未插紧）
3. 重新插紧后，PVE 识别到硬盘但自动加载了 UAS 驱动
4. VM 103 没有配置 USB 直通，飞牛看不到硬盘
5. FNOS 存储池 3 告警，`/vol3` 离线

---

## 三、修复过程

### 步骤 1：确认硬盘和盒子完好

将新硬盘盒接入 MacBook，通过 `system_profiler SPUSBDataType` + `diskutil list` 确认：

```
UNITEK (ASMedia 174c:55aa)
→ Fanxiang S100Pro 1TB
→ disk4s1: Linux_RAID 分区
→ S.M.A.R.T. Verified ✅
```

硬盘和数据完好，盒子正常。

### 步骤 2：插回 PVE，发现 UAS 驱动问题

PVE 识别到硬盘盒，但新 ASMedia 芯片被自动加载 UAS 驱动：

```
scsi host2: uas
sda: Fanxiang S100Pro 1TB (954G)
sda1: Linux_RAID
```

新芯片 `174c:55aa` 不在旧 UAS 黑名单（`152d:0578:u`）里。

### 步骤 3：在线切换 UAS → usb-storage

得益于之前操作经验，直接在线操作，无需重启：

```bash
# 1. 解绑 UAS 驱动
echo -n "2-2:1.0" > /sys/bus/usb/drivers/uas/unbind

# 2. 添加新芯片的 UAS 禁用 quirk
echo "174c:55aa:u" > /sys/module/usb_storage/parameters/quirks

# 3. 绑定到 usb-storage
echo -n "2-2:1.0" > /sys/bus/usb/drivers/usb-storage/bind

# 4. 触发 SCSI 扫描
echo "- - -" > /sys/class/scsi_host/host2/scan
```

验证：
```
usb 2-2: UAS is ignored for this device, using usb-storage instead
usb-storage 2-2:1.0: USB Mass Storage device detected
usb-storage 2-2:1.0: Quirks match for vid 174c pid 55aa: c00000
scsi host2: usb-storage 2-2:1.0
```

### 步骤 4：更新 GRUB 永久固化

```bash
# /etc/default/grub
GRUB_CMDLINE_LINUX="usb-storage.quirks=152d:0578:u,174c:55aa:u"

update-grub
```

### 步骤 5：配置 VM 103 USB 直通

```bash
qm set 103 -usb1 host=2-2
```

### 步骤 6：FNOS 侧恢复存储池

```bash
# 阵列从 auto-read-only 切为读写
sudo mdadm --readwrite /dev/md126

# /vol3 目录被删除过，需要重建
sudo mkdir -p /vol3

# 挂载
# 找到对应的 LVM 卷路径（可通过 sudo lvs 查看）
sudo mount /dev/<vg-name>/<lv-name> /vol3

# 写入 fstab 永久挂载（UUID 通过 sudo blkid 查看）
echo 'UUID=<filesystem-uuid> /vol3 ext4 defaults 0 2' | sudo tee -a /etc/fstab
```

---

## 四、最终状态

### 1. FNOS 存储架构
```
sdc1 → md126 (active raid1) → LVM → ext4 → /vol3
容量：950G  已用：256G  可用：693G  使用率：27%
```

### 2. 修复效果确认
- PVE 宿主机：usb-storage 驱动，UAS 已禁用
- VM 103 配置：`usb1: host=2-2` USB 直通
- FNOS `md126` 状态：`active raid1 [U]`
- `/vol3` 正常挂载，数据完整
- fstab 已写入，重启后自动挂载

### 3. UAS 黑名单清单

| VID:PID | 芯片 | 品牌 |
|---------|------|------|
| `152d:0578` | JMicron JMS578 | Ugreen |
| `174c:55aa` | ASMedia ASM1153E | UNITEK |

---

## 五、待办事项

| 优先级 | 事项 | 说明 |
|--------|------|------|
| 高 | 择机重启 PVE 宿主机 | 使 GRUB 内 UAS 禁用参数永久生效（当前在线切换在重启后会丢失，但 GRUB 已配置好，重启自动恢复） |
| 低 | 数据备份 | 为 `/vol3` 核心数据配置异地备份 |

---

## 六、经验回顾

1. **更换硬盘盒后一定要检查 VID:PID**，新芯片不在旧 UAS 黑名单里是常见坑
2. **消费级 ASMedia 桥接芯片在 PVE 虚拟化场景同样需要禁用 UAS**，虽然比 JMicron 稳定，但保险起见一律 `usb-storage.quirks` 覆盖
3. **在线切换 UAS → usb-storage 完全可行**，先写 quirk 再重新绑定驱动，无需重启宿主机
4. **`/vol3` 目录可能被 FNOS 清理掉**，如果挂载失败先检查挂载点是否存在
5. **fstab 要用 `tee -a` 追加**，小心不要覆盖已有行，注意换行
6. **FNOS `md126 auto-read-only` 是安全机制**，不是故障，手动 `--readwrite` 即可
