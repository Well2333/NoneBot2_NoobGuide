# 使用NoneBot2搭建QQ机器人

先做一下自我介绍，我是Well404，是一个苦逼大二狗，兴趣是随便编点程，Python也是2020年5月我开始断断续续自学的，所以难免有疏漏或错误，还请各位大佬海涵和指点。
>我的B站账号:[Well404](https://space.bilibili.com/33138220)
>我的GitHub账号:[Well2333](https://github.com/Well2333)

关于NoneBot2，我们先来引用一下[官方文档](https://v2.nonebot.dev/)的描述

>NoneBot2 是一个可扩展的 Python 异步机器人框架，它会对机器人收到的事件进行解析和处理，并以插件化的形式，按优先级分发给事件所对应的事件响应器，来完成具体的功能。
>除了起到解析事件的作用，NoneBot 还为插件提供了大量实用的预设操作和权限控制机制。对于命令处理，它更是提供了完善且易用的会话机制和内部调用机制，以分别适应命令的连续交互和插件内部功能复用等需求。
>
>得益于 Python 的 asyncio (opens new window)机制，NoneBot 处理事件的吞吐量有了很大的保障，再配合 WebSocket 通信方式（也是最建议的通信方式），NoneBot 的性能可以达到 HTTP 通信方式的两倍以上，相较于传统同步 I/O 的 HTTP 通信，更是有质的飞跃。
>
>需要注意的是，NoneBot 仅支持 Python 3.7+

看起来晦涩难懂？其实要点主要有以下几个：
>1. Nonebot2是基于python3.7及以上运行的
>2. 框架整体基于异步io
>3. 有完善的预设指令，可减轻开发者的工作强度

看到这里，你可能会大呼过瘾。如此优秀的框架已经迫不及待的想要使用了呢！
且慢且慢，请先随我一起进行学习，慢慢上手

## 更新计划
第一章：Hello World——初识NoneBot2
>在本章节，你将学会安装并配置你的第一个QQ机器人实例

第二章：哈？你说啥？——基础插件编写指南
>在本章节，你将学会编写最基本的文字和图片的交互的插件

第三章：在？来张setu？——进阶插件编写指南
>在本章节，你将学会使机器人能够响应更多的事件，发送更多种类的信息，以及插件间通信、定时任务框架等。

第四章：你认真读README了吗？——常用API总结
>在本章节，我将梳理nonebot及gocqhttp的常用api，并附上实例


## 引言（可以不看）
在正式开始教程之前，我想对以下几个概念进行说明：

### 初步认识前端、后端
我们先思考一下，如果让你去实现一个QQ机器人，我们需要什么样的程序？
如果你现在没有头绪，我们不妨先去看一下这个例子
>1. 顾客去饭店点了一份鱼香肉丝
>2. 服务员将你点鱼香肉丝的这件事汇报给后厨
>3. 后厨的厨师炒出了鱼香肉丝
>4. 服务员从后厨取出了鱼香肉丝
>5. 服务员把鱼香肉丝交给顾客

在这个过程中，顾客所接触到的人只有服务员，但服务员本身并不进行炒菜的工作，而厨师又始终不知道具体是谁哪位顾客了什么菜，那么这份菜究竟是怎么交付到这位顾客的手中的呢？

我们再回顾一下第二条与第四条，这两条中服务员与厨师进行了“交互”，通过这种方式将厨师所需要的信息进行了传递。并且将厨师处理完毕的菜交到顾客的手中。

这种结构最大的优点是高度的可替代性和稳定性，例如今天厨师A生病了就可以换用厨师B，服务员Ｃ的老婆生孩子了就可以让服务员D多干一些。而不会出现因为某个环节的部分功能缺失导致全盘崩溃。

我们将目光放回到QQ机器人上，用上述的思路重新进行思考，把顾客、服务员与厨师替换成腾讯的服务器、前端、后端。

>1. 前端从腾讯的服务器上接收用户的信息（或其他）
>2. 将信息传递给后端
>3. 后端对信息进行分析，调用对应的模块进行处理
>4. 后端将处理结果推送给前端
>5. 前端将处理后的结果重新发送给腾讯的服务器（或其他）

怎么样，是不是一目了然了呢？

在本篇教程中，我们使用的的前端是[go-cqhttp](https://docs.go-cqhttp.org/)，后端是[NoneBot2](https://v2.nonebot.dev/)，他们将很很好的完成上述的工作。

### WebSocket
>WebSocket是一种在单个TCP连接上进行全双工通信的协议。WebSocket使得客户端和服务器之间的数据交换变得更加简单，允许服务端主动向客户端推送数据。在WebSocket API中，浏览器和服务器只需要完成一次握手，两者之间就直接可以创建持久性的连接，并进行双向数据传输。

在go-cqhttp与NoneBot的信息交互中，我们使用的就是反向WebSocket连接，也就是说go-cqhttp会主动去寻找nonebot2的程序，并将消息通过ws推送给nonebot2，因此相对于传统的HTTP连接，这种方法明显效率更高，延迟更低。

### OneBot标准
>[OneBot标准](https://github.com/howmanybots/onebot/)是一个聊天机器人应用接口标准，旨在统一不同聊天平台上的机器人应用开发接口，使开发者只需编写一次代码即可应用到多种机器人平台。

在go-cqhttp与NoneBot的信息交互中，通讯的内容是基于OneBot的标准的改版，具体可以参考[go-cqhttp的文档](https://docs.go-cqhttp.org/)。

我们先回顾一下前文中的饭店。
如果这个饭店是一个法餐馆，服务生是中国人而厨师是法国人，二者语言并不互通，那么还能够完成点餐的任务吗？
明显不能，所以我们会人为要求服务生与厨师都用同一种语言进行交流，而这种语言标准就可以帮助二者有效的沟通。

在两个程序的沟通中，我们需要二者能够识别对方的信息，而不同的开发者不一定会使用一样的交流方式。因此随着OneBot标准的诞生，开发者可以使用这个标准的规范化的语言，那么语言不通的问题就迎刃而解了。
