# Homebrew使用指南

 `brew outdated` ：查看所有可更新软件。

`brew cleanup` ：清除旧版本软件。

`brew upgrade` ：更新所有的软件，或者 `brew upgrade [软件名]`更新指定软件。

`brew update` ：更新 Homebrew。



## 管理后台软件

诸如 Nginx、MySQL 等软件，都是有一些服务端软件在后台运行，可以使用 `brew services` 命令来进行管理

- `brew services list`： 查看所有服务
- `brew services run [服务名]`: 单次运行某个服务
- `brew services start [服务名]`: 运行某个服务，并设置开机自动运行。
- `brew services stop [服务名]`：停止某个服务
- `brew services restart`：重启某个服务。



## 检查 Homebrew 环境

如果你的 Homebrew 没有办法正常的工作，你可以执行 `brew doctor` 来开启 Homebrew 自带的检查，从而确认有哪些问题，并进行修复。



##  换源

http://mirrors.ustc.edu.cn/help/homebrew-bottles.html

```bash
echo 'export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.ustc.edu.cn/homebrew-bottles/bottles' >> ~/.zshrc
source ~/.zshrc
```

