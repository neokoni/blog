---
title: 手把手教你开设自己的Minecraft Java版服务器
date: 2024-02-02 20:48:33
tags:
    - 教程
    - Minecraft
cover: ../picture/开设java版服务器教程/banner.png
banner:
    type: img
    bgurl: ../picture/开设java版服务器教程/banner.png
---
## 分类
Minecraft的Java版的服务端主要分为四个类别:纯净端，插件端，模组端，混合端(支持模组与插件)。  
其中纯净端为Minecraft官方的服务端，更新最快且无其他附加功能，无法安装插件与模组，也不带优化，仅官方配置文件可修改。   
插件端：在原版端的基础上添加补丁实现加载插件与额外的配置选项。  
模组端：可安装客户端的模组，且一般要求与<code>客户端</code>模组与版本号都与<code>服务端</code>相同，<code>常常需要配合特定的客户端使用</code>.   
混合端：同时支持模组与插件，有<code>基于插件端开发</code>的兼容模组，有<code>基于模组端开发</code>的基于插件，以服务端本身同时兼容两者划分  
插件端以CraftBukkit为起点，再基于前者衍生出新的服务端，如spigot,paper,leaves等服务端，它们以前者为基础，加以自己的<code>优化</code>并结合自己的<code>api</code>对开发者以及服务器中<code>更多的可设置项</code>实现更多的功能  
模组端常见forge,fabric以及其他的可加载模组的服务端，其中fabric在很长一段时间内由于其模组辅助和优化被用作生电服务器的核心。  
混合端：通常基于的那一方兼容性更好，例如基于插件端就可能对插件兼容好一点，但是大多数方面的兼容都是无感的，但是这一类服务端的稳定性一直是个讨论的问题
## 获取服务端
官方端：[Minecraft官方](https://www.minecraft.net/zh-hans/download/server) [GetBukkit](https://getbukkit.org/download/vanilla)  
插件端：[Spigot](https://getbukkit.org/download/spigot) [Paper](https://papermc.io/downloads/paper) [Leaves](https://leavesmc.top/downloads/leaves) [Purpur](https://purpurmc.org/)  
模组端：[Forge](https://files.minecraftforge.net/) [Fabric](https://fabricmc.net/use/server/)
{% note info %}
关于Forge，你首先要下载Installer，下载完成后双击或使用<code>java -jar 你的forge安装器文件</code>来启动安装程序，选择<code>Install server</code>，选择好服务端的目录，它会自动生成服务端
{% endnote %}
混合端：你可能需要自行选择并下载服务端:(
## 安装JAVA
以上提到的服务端都需要Java才能运行这些Java服务端，游玩Mineraft的你应该早已安装。没有的话参照[这里](https://www.craft233.top/Install_game/)安装对应自己系统的Java。
{% note info %}
Minecraft Java版从2021年1月27日开始不能使用Java 8进行启动。在此之前，Mojang Studios一直支持Java 8，但由于Java 8不再获得Oracle的官方支持，Mojang Studios决定停止支持Java 8，以确保游戏的安全性和稳定性。  
具体来说，Mojang Studios在2021年1月27日发布的Minecraft Java Edition 1.16.5版本中，不再支持Java 8。
{% endnote %}
### 我有多个版本Java?
你可以在启动的时候将<code>java</code>换成特定版本的安装路径，比如在Windows中默认为<code>C:\Program Files\Java\jdk-17\bin\java</code>，如果是Linux使用包管理器安装则因该为<code>/usr/lib/jvm/java-17-openjdk/bin/java</code>  
其中具体路径要根据自己情况进行更改，但总的来说，你可以在<code>C:\Program Files\Java\</code>或<code>/usr/lib/jvm/</code>找到所有的Java版本(如果都是默认配置安装)
## 启动服务端
创建一个文件夹，将<code>.jar</code>结尾的服务端丢进去，防止文件混乱  
你拿到了一个以<code>.jar</code>结尾，接下来就是启动它，虽然大多数人的图形化界面都支持双击打开<code>.jar</code>文件，但这永远不是推荐的启动Minecraft服务端的方法。  
如果你直接这样，你不能设置最大内存，无法使用一些优化参数，对于没有gui的服务端你甚至要找打它的进程才能关闭，创建一个启动脚本无疑是更好的选择  
如果你是Windows用户，在你的服务端同文件夹<code>新建文本文档</code>，重命名为<code>start.bat</code>并写入一下内容作为启动脚本
```
java -jar -Xmx8G 你的服务端文件名 nogui
pause
```
如果你是Linux/Macos用户，打开终端，在你的服务端同文件夹新建<code>start.sh</code>，并写入一下内容作为启动脚本并运行<code>chmod +x start.sh</code>给予运行权限，并使用<code>./start.sh</code>运行
```
java -jar -Xmx8G 你的服务端文件名 nogui
```
## 正式运行
一切就绪，第一次运行服务端会直接推出并要求你同意一个协议，即<code>Minecraft 最终用户许可协议</code>，你可以在[Minecraft官网](https://aka.ms/MinecraftEULA)上找到详细内容  
打开eula.txt，将<code>eula=false</code>的<code>false</code>改为<code>true</code>即表示同意此协议  
再次运行启动脚本，即可正常启动服务器
## 保持后台运行
使用但命令行管理是一个原始的办法，且远程管理并不便携，采用<code>网页管理面板管理是个更好的选择，这里推荐[MCSManager](https://mcsmanager.com/)  
如果你是Linux用户，在终端运行以下命令，它会自动完成安装，浏览器访问<code>本机ip:23333</code>即可打开网页面板。
```
sudo wget -qO- https://gitee.com/mcsmanager/script/raw/master/setup_cn.sh | bash
```
第一次打开会有一段新手引导，如果你第一次使用，可以先看看。  
完成后点击<code>应用实例</code>，选择<code>创建应用</code>，选择<code>创建Minecraft Java版游戏服务器</code>，你可以选择<code>现有目录</code>，也可以选择<code>上传单个服务器文件</code>并使用<code>命令助手</code>生成启动命令，如果你什么也没有，可以选择<code>一键部署</code>，就是服务端较少   
如果你是<code>选择已有的目录</code>，请复制你的<code>Minecraft服务端文件夹路径</code>并填入到相对应的位置，应用名称按你的喜好填写，启动命令你可以直接填入你<code>原启动脚本</code>里的内容，也可以使用<code>命令助手</code>生成，接着点击<code>完成</code>即可创建。  
## 优化运行Flag(选做)
你可以在启动命令中添加一些flag优化服务端的运行，但不是所有的flag都是正向优化  
[PaperMC](https://docs.papermc.io/paper/aikars-flags)有一个推荐的Flag配置
## 开启离线模式(非正版服)
编辑<code>server.properties</code>，找到<code>online-mode</code>一项，将<code>true</code>改为<code>false</code>即可  
关闭后一些功能会失去，如玩家皮肤为默认皮肤，TAB栏不再显示玩家头像等，皮肤问题可以使用[skinsrestorer](https://skinsrestorer.net/)恢复  
到此，你已经在本地搭建了一个Minecraft服务器，或者可以通过<code>ipv6地址</code>加入
## 与他人共享(选做)
如果你家拥有动态公网ipv4/ipv6，对于ipv4你需要查看光猫/路由器的管理界面获取到的ip地址。如果它与你在ip查询网站显示的ip一致，即它为你家/你电脑的公网ipv4/ipv6地址。  
如果是ipv4地址，你需要在你的宽带拨号终端开放你电脑的服务器端口，服务器端口以<code>server.properties</code>中的<code>server-port</code>为准，如不会，请[Bing一下](https://cn.bing.com/search?q=%E8%B7%AF%E7%94%B1%E5%99%A8%E7%AB%AF%E5%8F%A3%E6%98%A0%E5%B0%84)  
打开[ipw.cn](https://ipw.cn/)，如果显示你拥有ipv6地址，即可使用此地址联机，它在添加服务器里的地址写法为<code>[ipv6地址]:25565</code>
### 使用Frp穿透(选做)
如果你和你的伙伴均没有ipv6或只有一方有，或者你没有公网ipv4，你可能需要<code>虚拟局域网</code>或者<code>frp</code>等方式才能进行联机  
使用Frp参照[SakuraFrp Docs](https://doc.natfrp.com/app/mc.html)，你仍可以自由选择Frp服务商，教程理论上Frp通用

