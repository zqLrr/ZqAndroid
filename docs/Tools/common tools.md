# [Oh-My-zsh](https://ohmyz.sh/)

> zsh 的搭配工具 提供快捷开发的工具

### Basic Installation

Oh My Zsh is installed by running one of the following commands in your terminal. You can install this via the command-line with either `curl`, `wget` or another similar tool.

| Method    | Command                                                      |
| --------- | ------------------------------------------------------------ |
| **curl**  | `sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"` |
| **wget**  | `sh -c "$(wget -O- https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"` |
| **fetch** | `sh -c "$(fetch -o - https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"` |

Alternatively, the installer is also mirrored outside GitHub. Using this URL instead may be required if you're in a country like China or India (for certain ISPs), that blocks `raw.githubusercontent.com`:

| Method    | Command                                           |
| --------- | ------------------------------------------------- |
| **curl**  | `sh -c "$(curl -fsSL https://install.ohmyz.sh/)"` |
| **wget**  | `sh -c "$(wget -O- https://install.ohmyz.sh/)"`   |
| **fetch** | `sh -c "$(fetch -o - https://install.ohmyz.sh/)"` |

一.主题

1.安装主题

Omz 会提前预装一些Theme,位置在`~/.oh-my-zsh/themes`,没有的需要先安装

2.配置环境变量

```zsh
vim ~/.zshrc
themes=[#在这里添加相应的插件名称]
source ~/.zshrc
```

二.插件

> 插件是其主要工具，使用新的插件一般需要以下几步

1.保证使用的工具插件主体已安装，比如git plugin,需要先安装主体git

2.安装plugin

Omz 会提前预装一些plugin,位置在`~/.oh-my-zsh/plugins`

可以查看需要使用的plugin 是否已安装，没有安装，需要安装一下，插件详情看https://github.com/ohmyzsh/ohmyzsh/tree/master/plugins

3.配置环境变量

```zsh
vim ~/.zshrc
plugins=(
#在这里添加相应的插件名称
)
source ~/.zshrc
```

