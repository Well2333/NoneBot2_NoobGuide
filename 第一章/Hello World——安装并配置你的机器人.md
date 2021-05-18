# Hello World——安装并配置你的机器人

## 一键安装并部署机器人（推荐）
对于完全新手来说，你可以使用我提供的有图形化界面的[一键安装工具](https://www.bilibili.com/video/BV1cp4y147Uw/)，这将极大地避免一些很低级的问题。
值得注意的是，本工具仅适用于Windows平台，且最好是Win10，Win7及以下很有可能发生预期外的错误

## 安装机器人所需要的程序
如果你使用了[一键安装工具](https://www.bilibili.com/video/BV1cp4y147Uw/)，那么此板块的操作可以省略。
### 安装NoneBot2
首先，我们需要提前安装python3.7或以上版本，可以在[一键安装工具](https://www.bilibili.com/video/BV1cp4y147Uw/)中找到详细的视频教程。
在安装完成后，我们使用快捷键Win+X，点击“Windows PowerShell(管理员)”或“命令提示符(管理员)”，并输入python --version以检查是否安装成功。
```shell
#Windows
python --version
#Linux
python3 --version
#你应该会得到类似这样的回复
Python 3.8.5
```
安装成功之后，我们可以根据[官方文档](https://v2.nonebot.dev/guide/installation.html)进行安装，依次在PowerShell中执行下列命令
```shell
#如果你曾经安装过nb1,先卸载nb1
pip uninstall nonebot
#安装依赖
pip install nb-cli
pip install nonebot-adapter-cqhttp
#如果你是Linux用户，仅需将pip改为pip3即可
```
如果你使用[一键安装工具](https://www.bilibili.com/video/BV1cp4y147Uw/)进行安装，程序将会自动执行上述安装过程。

### 安装go-cqhttp
在go-cqhttp的GitHub项目的[releases页面](https://github.com/Mrs4s/go-cqhttp/releases)中下载版本高于1.0.0的release即可（版本请根据自己的操作系统进行选择）
此处附上[官方文档](https://docs.go-cqhttp.org/guide/quick_start.html)的指南：
>Windows下32位文件为 go-cqhttp-v*-windows-386.zip
Windows下64位文件为 go-cqhttp-v*-windows-amd64.zip
Windows下arm用(如使用高通CPU的笔记本)文件为 go-cqhttp-v*-windows-arm.zip
Linux下32位文件为 go-cqhttp-v*-linux-386.tar.gz
Linux下64位文件为 go-cqhttp-v*-linux-amd64.tar.gz
Linux下arm用(如树莓派)文件为 go-cqhttp-v*-linux-arm.tar.gz
MD5文件为 *.md5 , 用于校验文件完整性

## 配置机器人
如果你使用了[一键安装工具](https://www.bilibili.com/video/BV1cp4y147Uw/)，那么此板块的操作可以省略。
### go-cqhttp配置
###### 生成配置文件
将客户端文件解压并置于一个空文件夹后，首先开启一次该客户端

Windows用户可以双击运行，但有可能会出现不可预期的错误，因此我推荐使用bat运行：
>首先将你下载的exe文件重命名为go-cqhttp.exe，并将其置于一个新的文件夹，在go-cqhttp的根目录下（即同一文件夹下）新建一个文本文档，将`go-cqhttp.exe`写入并保存，退出后将文本文档重命名为`xxx.bat`(xxx是文件名，你可以随意起一个，例如`start.bat`)，以后双击xxx.bat即可启动go-cqhttp

如果你是Linux用户，你需要将下载的文件进行解压缩后才可执行。
```shell
cd 文件目录
tar -xzvf 文件名
```
当然，你可以直接用7-zip等软件在你的Windows电脑上解压完毕之后再将此文件上传至你的服务器，这样将会十分方便。
然后，运行一次该程序
```shell
cd 文件目录
./go-cqhttp
```
此时，系统将会显示
```
[WARNING]: 尝试加载配置文件 config.yml 失败: 文件不存在
[INFO]: 默认配置文件已生成,请编辑 config.yml 后重启程序.
```
这时，我们就可以关闭客户端了
###### 填写配置文件
我们找到刚刚生成的config.yml，打开后可看到一大堆配置信息（不同版本间可能会有细微的差异，但影响不大）
由于配置信息过长，故不在此再次展示，如有需要可以参考[官方文档](https://docs.go-cqhttp.org/guide/config.html)的信息
我们需要修改以下几处（不是让你把其他的全都删掉，而是在这几处进行修改）
```
account: # 账号相关
  uin: 1233456 # QQ账号
```
这里将123456替换为你的机器人的QQ号，建议使用一个新建的账号。
此处不建议直接使用密码登录，由于第一次登录必定需要手机端确认，填入密码不仅会有泄露密码的风险，甚至还会有一些意想不到弊端（例如滑块验证）。
go-cqhttp使用的是智能手表端，如果你有智能手表请勿登录此QQ。
同时，新账号很有可能被风控，详见下一节的说明。
```
servers:
  # HTTP 通信设置
  - http:
      # 是否关闭正向HTTP服务器
      disabled: true #第一处修改！！！

  - ws-reverse:
      # 是否禁用当前反向WS服务
      disabled: false #第二处修改！！！
      # 反向WS Universal 地址
      # 注意 设置了此项地址后下面两项将会被忽略
      universal: ws://127.0.0.1:xxxx/cqhttp/ws #第三处修改！！！
```
此处一共有三处修改，前两处直接照着改就行，第三处将xxxx替换为端口号，其余照抄。
>[端口号](https://baike.baidu.com/item/%E7%AB%AF%E5%8F%A3%E5%8F%B7/10883658)建议从5000-65535取值，并且尽量避免一些非常常用的数字，例如8888、6666等，如果端口被占用的话程序是无法正常工作的
另外，请务必记住此处的端口号，我们在之后还需要用到这个端口号
###### 再次运行客户端
开启步骤同第一次一样，只不过这次的提示信息将会更多。
在客户端尝试登录的时候，如果你是第一次尝试登录此账号，你需要扫码登陆这个账号（使用你需要登陆的账号扫码）.
``` 
[INFO]: アトリは、高性能ですから! 
```
当出现这条信息的时候，你的go-cqhttp客户端就在正常运行了。

### NoneBot2配置
###### 使用脚手架创建机器人（个人不推荐使用）
>注意，此方法简单易用，但是在通过SSH远程连接Linux系统下会存在一些问题，例如无法使用方向键等，如果无法使用可不必太过纠结。
同时，使用这种方式创建的机器人由于配置项较多，反而不太利于新手学习，因此我推荐使用手动创建机器人，操作将会极大地简化

首先，创建一个文件夹，并在命令行（或SSH）中输入`cd 文件夹路径`定位到此文件夹，
然后，输入`nb`，会显示这样的画面
```
d8b   db  .d88b.  d8b   db d88888b d8888b.  .d88b.  d888888b
888o  88 .8P  Y8. 888o  88 88'     88  `8D .8P  Y8. `~~88~~'
88V8o 88 88    88 88V8o 88 88ooooo 88oooY' 88    88    88
88 V8o88 88    88 88 V8o88 88~~~~~ 88~~~b. 88    88    88
88  V888 `8b  d8' 88  V888 88.     88   8D `8b  d8'    88
VP   V8P  `Y88P'  VP   V8P Y88888P Y8888P'  `Y88P'     YP


Welcome to NoneBot CLI!
? What do you want to do?  (Use arrow keys)
 ❯ Show Logo
   Create a New Project
   Run the Bot in Current Folder
   Create a New NoneBot Plugin
   List All Published Plugins
   Search for Published Plugin
   Install a Published Plugin
   Update a Published Plugin
   Remove an Installed Plugin
   Create a Custom Adapter
   List All Published Adapters
   Search for Published Adapters
   Build Docker Image for the Bot
   Deploy the Bot to Docker
   Stop the Bot Container in Docker
```
然后使用方向键控制左侧的方框（或箭头），停在`Create a New Project`左侧后按回车。
```
[?] Project Name:
```
此处输入你的机器人的名字（随便起一个就行，建议使用英文和数字，尽量避免其他语言，包括中文，此处我起的名字为w404），然后按回车。
```
[?] Where to store the plugin?  (Use arrow keys)
   1) In a "w404" folder
 ❯ 2) In a "src" folder
```
选择第二项（即src）后回车。
```
Load NoneBot Builtin Plugin?  (y/N)
```
直接按y（当然，你也可以选择N）
```
[?] Which adapter(s) would you like to use?  (<up>, <down> to move, <space> to select, <a> to toggle, <i> to invert)
 ❯○ cqhttp
  ○ ding
  ○ mirai
  ○ gocq
```
此处选择cqhttp（gocq也可以，功能基本相同）。即光标停在cqhttp左侧时按空格选中，然后按回车确认。
然后程序会为你安装依赖库，不过我们已经通过此命令`pip install nonebot-adapter-cqhttp`事先安装好了，所以安装将会较快。
然后程序就会在对应文件中生成该机器人的文件夹。
再次强烈建议使用手动创建的方式重新创建一个新的机器人来使用！！！
具体的配置方法待续（其实和下面的差不多）。
###### 手动创建机器人
>如果你无法直接创建机器人，你可以手动创建一个机器人。

与使用脚手架安装相同，你需要一个新的文件夹，此处我的文件夹名为`Bot404`，你可以任意起你喜欢的名字，但尽量使用英文和数字的组合，不要使用中文。
在文件夹中新建一个`bot.py`，并向其中写入以下代码。
```python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
import nonebot
from nonebot.adapters.cqhttp import Bot as CQHTTPBot
#初始化nb
nonebot.init()
app = nonebot.get_asgi()
#连接驱动
driver = nonebot.get_driver()
driver.register_adapter("cqhttp", CQHTTPBot)
#加载插件(除此处其他配置不建议更改)
nonebot.load_builtin_plugins()
nonebot.load_plugins('src/plugins')
#启动bot
if __name__ == "__main__":
    nonebot.logger.warning("Always use `nb run` to start the bot instead of manually running!")
    nonebot.run(app="__mp_main__:app")
```
在相同目录下创建`.env`文件，并写入
```
HOST=127.0.0.1
PORT=xxxx
DEBUG=False
SUPERUSERS=["123456","234567","345678"]
COMMAND_START=["/","!!"]
```
我们来逐行解释以下设置项
>HOST=127.0.0.1 
>程序运行的host，不要做任何更改

>PORT=xxxx
程序监听的端口，此处填写与刚才go-cqhttp一致的端口，否则程序将无法正常连接

>DEBUG=False 
如果开启此项设置，程序将会对bot.py文件夹及其子目录下的一切文件数据变化进行监控，并在变化后重启机器人，非常适合用于debug和调试，但并不利于日常使用，请在长期稳定运行的机器人中关闭此项设置

>SUPERUSERS=["123456","234567","345678"]
此处是机器人的超级用户名单，也就是管理员名单，可以设置多个用户，请注意此处的全部标点符号均是英文的！

>COMMAND_START=["/","!!"]
命令的起始符号，即以此列表中的所有符号作为起始的消息会被识别为“命令”，反之则不会
如果你想让机器人对所有的消息均识别为命令（极度不推荐），你可以在这个列表中写入一个空值，即[""]。

然后，在同文件夹下新建一个名为src的文件夹，并在其中再新建一个plugins的文件夹。
此时，我们的文件目录结构应为这样。
```
\---Bot404
    |   .env
    |   bot.py
    |
    \---src
        \---plugins
```
顺带一提，此目录结构是用命令生成的，详情可见[这里](https://zhuanlan.zhihu.com/p/130956933)。

## 运行机器人
大功告成，只欠东风！我们这时可以第一次运行我们的机器人了！
>如果你使用了[一键安装工具](https://www.bilibili.com/video/BV1cp4y147Uw/)，那么我们可以直接使用一键开启机器人，详见视频教程。

>另外，如果你使用SSH连接远程的Linux服务器，你可以使用screen或者其他方式同时运行多个程序并保证在下线后程序不会中断，具体使用方式很简单，请自行百度

首先使用命令行工具运行`bot.py`
```
#先进入工作目录
cd bot.py的根目录位置

#Windows
python bot.py
#Linux
python3 bot.py
```
然后看到如下提示：
```
05-10 23:56:12 [INFO] nonebot | NoneBot is initializing...
05-10 23:56:12 [INFO] nonebot | Current Env: prod
05-10 23:56:12 [INFO] nonebot | Succeeded to import "nonebot.plugins.echo"
05-10 23:56:13 [WARNING] __main__ | Always use `nb run` to start the bot instead of manually running!
05-10 23:56:13 [INFO] nonebot | Running NoneBot...
05-10 23:56:13 [INFO] uvicorn | Started server process [5884]
05-10 23:56:13 [INFO] uvicorn | Waiting for application startup.
05-10 23:56:13 [INFO] uvicorn | Application startup complete.
05-10 23:56:13 [INFO] uvicorn | Uvicorn running 
on http://127.0.0.1:xxxx (Press CTRL+C to quit) 
```
这时我们再去启动go-cqhttp
Windows可直接双击之前创建好的xxx.bat（start.bat），即可开启go-cqhttp。
Linux可以使用如下指令
```shell
cd go-cqhttp的根目录位置
./go-cqhttp
```
然后我们静待二者启动完毕后，使用超级用户账号向机器人的qq号发送
```
/echo Hello_World!
```
我们就会收到机器人的回复了！
恭喜你，你已经拥有了属于自己的QQ机器人了！