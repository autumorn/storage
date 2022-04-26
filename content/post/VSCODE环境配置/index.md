---
title: VSCODE环境配置
description: vscode c++开发环境配置
date: 2022-04-25
categories:
    - 配置
tags:
    - 工具
---
## VSCODE环境配置
>工欲善其事，必先利其器

### 1. vscode安装
官网(https://code.visualstudio.com/)根据不同的平台下载对应的安装包直接进行安装

### 2. c++编译环境插件安装
 * c/c++ : 可以进行c++支持标准的设置以及IntelliSense的相关设置
 * cMake : 基础的cmake插件功能
 * cMake Tools : 配置cmake编译功能，打开extension settings设置，主要配置cmake generator(可选择ninja、Unix Makefiles等generator),如果需要加参数运行，需要点击左侧debug按钮、创建launch.json文件并进行相关配置

### 3. markdown插件安装
#### Markdown All in One
安装完成后可以进行相关设置，根据配置选项说明进行调整

### 4. 远程及浏览器开发环境配置
vscode远程开发有两种方式：
* 本地安装vscode通过remote ssh插件方式在vscode中连接远程服务器进行开发，需要在本地安装vscode软件
* 远程服务器配置code server，可以在本地浏览器中打开vscode进行开发，只需要浏览器就可随地进行开发

这里介绍下面一种的配置方式，实现在浏览器开发的功能，有一键安装与离线安装两种方式，下面分别进行介绍
#### 一键式安装
在服务器中利用如下命令进行安装：
```shell
[测试安装过程]
curl -fsSL https://code-server.dev/install.sh | sh -s -- --dry-run
[开始安装]
curl -fsSL https://code-server.dev/install.sh | sh
```
安装成功会有提示，在命令行执行code-server来查看是否安装功能，为了以后使用方便，可以利用systemctl注册服务使用，或者直接运行code-server
```shell
sudo systemctl enable --now code-server@$USER
```
> 现在可以访问 http://远程ip地址:8080. 你的密码可以在 ~/.config/code-server/config.yaml 中设置。如果访问不了，可以将config.yaml中的ip设置为0.0.0.0试一下，端口注意不要用已经被占用的端口，配置完成后在浏览器输入网址及密码即可进入vscode，其它操作同vscode本地

#### 离线安装
从github上搜索code server下载最新的deb安装包进行安装，安装完成配置步骤与在线安装一致
```shell
curl -fOL https://github.com/cdr/code-server/releases/download/v$VERSION/code-server_$VERSION_amd64.deb
sudo dpkg -i code-server_$VERSION_amd64.deb
sudo systemctl enable --now code-server@$USER
```
