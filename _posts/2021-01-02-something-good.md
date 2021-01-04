---
layout: post
title: "好物分享--软件" 
description: "分享过去几年遇到或用到的好工具或物件"
category: 生活
tags: []
---

看着自己几年前写的一些碎碎念文字还挺有意思的，尝试做一些分享，或长或短，权当记录。

想到啥写啥，没想到的下次补充。


##### 1. VSCode 远程开发
VIM用了很多年，配好了一套插件也用得挺顺手，但是遇到更加大型的工程项目时，跳转查询就有点力不从心。另外插件生态来说，VSCode 一堆开箱即用的插件，还是会让人心动。

随着公司推行一人一台开发机，并且开发机连外网不再是障碍，很自然地，就切换到了使用 [VSCode 远程开发](https://code.visualstudio.com/docs/remote/ssh)。具体使用网上有很多介绍的文章，这里不赘述。

体验来说就是爽，只需要在开发机配置好了繁杂的C++环境或Golang环境，就能随意切换客户端使用，在iMac没写完的代码，回家可以继续无缝使用MacBook Pro 继续写😁。

好用是好用，就是有点**吃内存**，不过还在可接受范围之内。

![picture 2](/assets/pic/blog/399406f2be070d38efb3c9de33bdf1de848749632a3f0ae3662201632744baa4.png)  


##### 2. Tabnine 代码补全
号称使用了深度学习来帮你更快地写代码，实际体验来说确实还行，比如对于Golang写又臭又长的struct的时候，能够自动补全相关的json tag。

唯一的**问题**是内存占用高，实例开多了，16G内存开发机也顶不住。有很多的相关的Issue，希望官方后续继续优化。

https://www.tabnine.com/

![picture 3](/assets/pic/blog/197e33a6f0c13fbc0d0ea6cf0ad1406ca4b6009a586aa5e399947e78bf74bbe9.png)  

##### 3. Adminer 数据库管理工具
[官方介绍](https://www.adminer.org/)是对phpMyAdmin的改良替代：
>Replace phpMyAdmin with Adminer and you will get a tidier user interface, better support for MySQL features, higher performance and more security. See detailed comparison.

关键特性：
>Adminer development priorities are: 1. Security, 2. User experience, 3. Performance, 4. Feature set, 5. Size.

实际用着确实还行，但php文件部署简单（当然现在用docker了部署什么都不算麻烦），功能丰富又不繁杂。对于日常的数据查询和修改 使用起来都很方便。

另外配合 pappu687 这款css主题界面会舒服很多。

**注意** 并不代表完全没有漏洞，需要及时更新最新版本。另外使用的时候最好再套一层basic auth，避免直接暴露，高压线警告⚠️。
![picture 4](/assets/pic/blog/0aafa69e292e4aaf8668dbbdb0fcdd1839bc8545ab009daed444dd610eabc3c8.png)  

##### 4. Thor HTTP 抓包嗅探分析&接口调试&断点重写
[App Store 链接](https://apps.apple.com/cn/app/thor-http-%E6%8A%93%E5%8C%85%E5%97%85%E6%8E%A2%E5%88%86%E6%9E%90-%E6%8E%A5%E5%8F%A3%E8%B0%83%E8%AF%95-%E6%96%AD%E7%82%B9%E9%87%8D%E5%86%99/id1210562295)

名字好长，我在比较早期购入的，现在的价格可能稍微高一点。总的来说是iOS上很好用的抓包分析工具，并且开发者还在不断更新更多新功能。

比如一个有趣的玩法是对任意html页面注入 `vConsole`或 `Eruda`调试工具，随心所欲调试别人的页面。具体来说就是对于请求，提供了丰富的判断逻辑和断点替换修改等能力。

建议直接购入[三件套](https://apps.apple.com/cn/app-bundle/thor-anubis-shu-%E7%BD%91%E7%BB%9C%E8%B0%83%E8%AF%95%E4%BC%98%E6%83%A0%E5%A5%97%E8%A3%85/id1379995720)，可以有更多玩法。

 
![picture 6](/assets/pic/blog/4019b66d8f4cc06536f635b2989921dee9317237c014305634218dc7a6ddd8c1.png)  

##### 5. curlconverter
网址：[https://curl.trillworks.com/](https://curl.trillworks.com/)

一个很简单的网页小工具，可以把Chrome 开发者工具（或其他别的地方）复制得到的cURL命令，转换为各种编程语言的代码片段，复制即可运行，简单修改即可完成相关数据拉取等工作。

还有类似的工具 https://mholt.github.io/curl-to-go/ ，针对Golang 做了更细致的支持，比如自动根据请求json，生成对应的结构体。

##### 6. 结语
其他想起来再补充。

