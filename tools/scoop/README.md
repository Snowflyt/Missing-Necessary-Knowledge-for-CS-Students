# Scoop - Windows 上的包管理器

相信大家一定遇到过这样的烦恼：

- 想装个 Java，发现有一堆版本，还要折腾环境变量等各种设置，心力憔悴
- 电脑上装了 Python 3.11，打开别人给的代码，发现引用了某一个只支持 Python 3.8 及之前版本的库，还得把 Python 删了重装
- 电脑上各种编程语言和相关工具在 C 盘里到处乱飞，在 Program Files、Users\\\<username\>和 AppData 里遍地开花，完全搞不清楚装在哪里以及它下载的文件在哪儿放着

这时候你就需要一个包管理器解决问题，一键安装和卸载、多版本共存、统一管理……如果有用过 macOS 的 homebrew、Ubuntu 的 apt、CentOS 的 yum 或 Arch 的 Pacman 的同学，一定能体验到对于一个经常和这些编程工具打交道的人来说，有一个包管理器是多么爽的一件事

那么 Windows 下有没有包管理器呢？当然是有的，而且比很多 Linux 下的包管理器还好用，它就是 Scoop。这是它的[官网](https://scoop.sh/)

![Scoop 官方网站](img/scoop.png)

我个人建议以管理员身份安装，并且将它默认的安装文件夹改到 C:\\Scoop\\，这样会方便管理一些。以管理员身份打开 Powershell，输入以下命令

```powershell
irm get.scoop.sh -outfile 'install.ps1'
.\install.ps1 -RunAsAdmin -ScoopDir 'C:\Scoop'
```

等待一会儿（可能需要挂代理），然后你就会发现 Scoop 已经自动装好了。现在再打开 Powershell，输入`scoop`，你应该能看到提示信息了。

> **Tips:**
> 如果你的网络状态不足以正常安装 Scoop 及访问 Scoop 仓库（Bucket），请搜索“Scoop 配置国内镜像”并按照相关教程自行配置镜像。

Scoop 默认的 main 仓库可以安装的软件比较少，我推荐添加下面几个仓库：

```powershell
scoop bucket add java
scoop bucket add extras
scoop bucket add dorado https://github.com/chawyehsu/dorado
```

其中 extras 是 Scoop 官方维护的仓库，包含了比 main 更多的软件包。而 dorado 是国内开发者维护的仓库，包含了许多适合国内用户使用的软件包。而 java，顾名思义，包含了许多与 Java 有关的软件包。

你可以用 Scoop 安装大多数编程领域的相关工具，包括但不限于：

```powershell
scoop install git
scoop install miniconda3
scoop install python
scoop install poetry
scoop install temurin8-jdk
scoop install openjdk11
scoop install openjdk17
scoop install maven
scoop install gradle
scoop install mingw
scoop install msys2
scoop install gcc
scoop install llvm
scoop install nodejs
scoop install npm
scoop install yarn
scoop install sqlite
scoop install draw.io
...
```

同时，你可以一键升级所有当前通过 Scoop 安装的软件：

```powershell
scoop update *
```

搜索软件包：

```powershell
scoop search python
```

卸载软件包：

```powershell
scoop uninstall python
```

你可以同时用 Scoop 安装多个版本的软件，如 Java：

```powershell
scoop install temurin8-jdk
scoop install openjdk11
scoop install openjdk17
```

在上面的例子中，我们同时安装了 Java 8、11 和 17。你可以运行`scoop reset xxx`来重新配置某一个软件包的环境，以实现“切换版本”的功能：

```powershell
scoop reset temurin8-jdk
java -version # openjdk version "1.8.0_362"
scoop reset openjdk17
java -version # openjdk version "17.0.2"
```

你也可以选择安装某个特定版本的软件包，并且让 Scoop 不再更新它的版本，以保持稳定：

```powershell
scoop install maven@3.6.3
scoop hold maven
```

添加`vesions`仓库以获取更多不同版本的软件包：

```powershell
scoop bucket add versions
scoop install python38 python
scoop reset python38
python --version # Python 3.8.10
scoop reset python
python --version # Python 3.11.2
```

你甚至可以自定义仓库，或者寻找别人做好的仓库，用 Scoop 管理你电脑上几乎一切软件。不过，讲座时间有限，我就不展开了，大家感兴趣的话可以去看看 Scoop 的[官方文档](https://scoop-docs.vercel.app/docs/getting-started/Quick-Start.html)
