# Ubuntu 快速配置

- ##### 更新

  ```bash
  apt update
  apt upgrade
  ```

  > 了解 apt 和 apt-get 的区别：[中文](https://www.eet-china.com/mp/a36246.html) [英文](https://itsfoss.com/apt-vs-apt-get-difference/)

- ##### 安装 git

  ```bash
  apt install git
  ```

- ##### 安装 zsh

  ```bash
  apt install zsh
  ```

- ##### 将 zsh 设置为系统默认 shell

  ```bash
  chsh -s /bin/zsh
  ```

- ##### 安装 curl

  ```bash
  apt install curl
  ```

- ##### 安装 oh-my-zsh

  ```bash
  sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
  ```

  > 安装 oh-my-zsh 前要安装好 zsh、curl、git

- ##### 安装 neofetch

  ```bash
  apt install neofetch
  ```

#### 自动化脚本

```bash
#!/bin/bash

# 执行命令前输出打印命令
set -x

# 更新
apt update
echo y | apt upgrade

# 安装 git
apt -y install git

# 安装 curl
apt -y install curl

# 安装 zsh 并设置为默认 shell
apt -y install zsh &chsh -s $(which zsh)

# 安装 oh-my-zsh
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)" "" --unattended


# 安装 neofetch
apt -y install neofetch
```
