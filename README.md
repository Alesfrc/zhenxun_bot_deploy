# 绪山真寻 Bot 部署流程

部署绪山真寻 Bot 到 QQ 群聊

![maven](https://img.shields.io/badge/python-3.8%2B-blue)
![maven](https://img.shields.io/badge/nonebot-2.0.0-yellow)
![maven](https://img.shields.io/badge/go--cqhttp-1.0.0-red)

<div align=center><img width="320" height="320" src="https://raw.githubusercontent.com/HibiKier/zhenxun_bot/main/logo.png"/></div>

<p align="center">
<a href="https://hibikier.github.io/zhenxun_bot/docs/installation_doc"><strong>安装文档 »</strong></a>
</p>

## 目录

- [注意事项](#注意事项)
- [开始安装](#开始安装)
  - [安装依赖](#安装依赖)
  - [Python 安装](#python安装)
  - [go-cqhttp 安装](#go-cqhttp安装)
  - [zhenxun_bot 安装](#zhenxun_bot安装)
- [启动 go-cqhttp](#启动go-cqhttp)
- [启动 zhenxun_bot](#启动zhenxun_bot)
- [管理&注意事项](#管理和注意事项)
- [相关链接](#相关链接)
- [关于 Nonebot 插件](#关于nonebot插件)

### 注意事项

安装真寻 bot，你需要具备
- 一定的基础，包括但不限于稍微熟悉 linux 或 windows cmd 命令行
- 一些[百度](https://baidu.com)/[Google](https://google.com) 的能力
- 一台服务器

### 开始安装

教程安装对象系统为 Ubantu 20.04 LTSC

#### 安装依赖

首先安装 wget、git、screen、ffmpeg 以便后续使用

```
sudo apt update && sudo apt install wget git screen ffmpeg -y
```

以及 pip3 和 poetry(若要安装 Python3.10 请跳过)

```
sudo apt install python3-pip -y
sudo pip3 install poetry
```

#### python安装

安装 Python3.10
<br />
Ubuntu 22.x 可跳过
<br />
(机器人插件有些需要 Python3.9 以上)

安装 PPA 需要的软件源

```
sudo apt update && sudo apt install software-properties-common -y
```

添加名为 deadsnake 的 PPA 源

```
sudo add-apt-repository ppa:deadsnakes/ppa
```

安装 Python3.10

```
sudo apt install python3.10 -y
```

查看系统已存在的所有 python 版本

```
ls -l /usr/bin/python*
```

```
update-alternatives --install /usr/bin/python3 python3 /usr/bin/python3.8 1
update-alternatives --install /usr/bin/python3 python3 /usr/bin/python3.10 2
```

选择默认版本

```
update-alternatives --config python3
```

安装 pip3

```
curl -sS https://bootstrap.pypa.io/get-pip.py | python3.10
```

安装 Poetry

```
pip3 install poetry
```

#### go-cqhttp安装

安装 [go-cqhttp](https://github.com/Mrs4s/go-cqhttp/releases) (目录在~/go-cqhttp)

```
mkdir ~/go-cqhttp && \
	cd ~/go-cqhttp && \
	wget https://github.com/Mrs4s/go-cqhttp/releases/download/v1.0.0-rc1/go-cqhttp_linux_amd64.tar.gz -O go-cqhttp_linux_amd64.tar.gz && \
	tar -zxvf go-cqhttp_linux_amd64.tar.gz
```

生成配置文件

```
cd ~/go-cqhttp && echo -e "3\n" | ./go-cqhttp
```

修改 qq 账号和 unuversal 在 config.yml ctrl+x 保存

```
nano config.yml
```

```
universal: ws://127.0.0.1:8080/onebot/v11/ws
```

在本地上运行 [go-cqhttp](https://github.com/Mrs4s/go-cqhttp/releases) 并登录，把 device.json 和 session.token 复制到服务器上

#### zhenxun_bot安装

安装 Postgresql 数据库

```
sudo apt update && sudo apt install postgresql postgresql-contrib -y
```

创建数据库和用户（依次输入）

```
sudo su - postgres  # 切换用户
psql
        #  用户名↓                # 密码↓
# CREATE USER uname WITH PASSWORD 'zhenxun';      # 创建用户（可不执行）

                                  # 密码↓
alter user postgres with password 'zhenxun_bot';    # 修改postgres的密码

        # 数据库名称↓    所有者(用户名)↓
CREATE DATABASE zhenxun_bot OWNER postgres;             # 创建数据库
```

退出数据库并切换到你的常用 Ubuntu 账号

```
exit
```

git clone [真寻](https://github.com/HibiKier/zhenxun_bot)源码

```
cd && git clone https://ghproxy.com/github.com/HibiKier/zhenxun_bot.git
```

修改.env.dev 配置

```
cd ~/zhenxun_bot
nano .env.dev
```

进入 poetry 虚拟环境

```
poetry shell
```

安装依赖

```
poetry install
```

安装完检查

```
poetry show
```

安装 playwright 额外的系统环境

```
playwright install chromium
playwright install-deps chromium
```

填写数据库数据

```
cd configs/
nano config.py

############################################################
### 如果你是与教程一模一样的命令代码，且数据库也在该服务器上 #
############################################################
# 可以直接复制以下配置
bind: str = ""  # 数据库连接链接
sql_name: str = "postgres"
user: str = "postgres"
password: str = "zhenxun_bot"
address: str = "127.0.0.1"
port: str = "5432"
database: str = "zhenxun_bot"
```

真寻 bot 依赖

```
sudo apt install -y \
    libgl1 \
    libglib2.0-0 \
    libnss3 \
    libatk1.0-0 \
    libatk-bridge2.0-0 \
    libcups2 \
    libxkbcommon0 \
    libxcomposite1 \
    libxrandr2 \
    libgbm1 \
    libgtk-3-0 \
    libasound2
```

安装中文字体

```
sudo apt-get install -y ttf-wqy-zenhei ttf-wqy-microhei fonts-arphic-ukai fonts-arphic-uming
sudo fc-cache -f -v
```

### 启动go-cqhttp

创建 screen

```
screen -R go-cqhttp
```

启动 go-cqhttp

```
cd ~/go-cqhttp
./go-cqhttp
```

Ctrl+A+D 退出虚拟终端

### 启动zhenxun_bot

创建 screen

```
screen -R zhenxun_bot
```

启动 zhenxun_bot

```
cd ~/zhenxun_bot
python3 bot.py
```

第一次启动会生成配置文件
<br />
配置文件在~/zhenxun_bot/configs 里的 config.yaml 文件
<br />
编辑并保存配置文件再次启动

```
cd ~/zhenxun_bot/configs/
nano config.yaml
python3 bot.py
```

启动后等待资源文件全部下载完成

### 管理和注意事项

运行真寻 bot 一定要进入虚拟环境!!!

```
poetry shell
```

打开 [go-cqhttp](https://github.com/Mrs4s/go-cqhttp/releases) 的虚拟终端

```
screen -r go-cqhttp
```

打开 [zhenxun_bot](https://github.com/HibiKier/zhenxun_bot) 的虚拟终端

```
screen -r zhenxun_bot
```

使用 Ctrl+A+D 退出 screen
<br />
使用 screen -ls 查看当前虚拟终端
<br />
使用 screen -r name -X quit 结束 screen

### 相关链接

[真寻 bot 的文档](https://hibikier.github.io/zhenxun_bot)
<br />
[真寻 bot 的仓库](https://github.com/HibiKier/zhenxun_bot)
<br />
[真寻 bot 的插件仓库](https://github.com/AkashiCoin/nonebot_plugins_zhenxun_bot)
<br />
[真寻 bot 的一键安装脚本](https://github.com/AkashiCoin/zhenxun_bot-deploy)
<br />
[真寻 bot 的 WebUI](https://github.com/HibiKier/zhenxun_bot_webui)

## 关于nonebot插件

zhenxun_bot 不兼容 Nonebot 插件
<br />
若需要装载 Nonebot 插件，需要另建一个 go-cqhttp 并创建 Nonebot 项目
