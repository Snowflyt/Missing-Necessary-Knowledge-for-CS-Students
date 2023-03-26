# 安装

我个人建议 Windows 用户通过 Scoop 安装 Vim，这样可以避免一些不必要的麻烦。关于 Scoop 的介绍参见[这里](../../tools/scoop.md)。

下面是使用 Scoop 安装 Vim 的命令：

```powershell
scoop install vim
```

对于 Linux 用户，一般正常的 Linux 发行版都是自带 Vi/Vim 的，所以不用额外安装。如果你用的是不正常的发行版，那我想你应该能够自己搞定。而对于 macOS 用户，很遗憾，我没用过 macOS，所以无法提供相关安装方法。

另外，使用 Scoop 安装的 Vim 默认不带下面将提到的 vimtutor，如果你需要，可以通过下面的命令安装：

```powershell
scoop install vimtutor
```

不过使用 Scoop 安装的 vimtutor 是英文版的，如果你需要中文版，还是建议自己下载一份中文版的 vimtutor。下面会提到。
