---
title: Linux编译安装GCC及多版本管理
description: GCC的编译与安装及版本管理
date: 2022-04-14
categories:
    - 工具
tags:
    - GCC
---
### GCC源代码编译安装
#### 检查及安装服务器上编译环境，以ubuntu为例
```shell
sudo apt-get install build-essential wget m4 flex bison
```
#### 下载需要的GCC版本源代码，可以利用wget下载，以10.3.0为例
```shell
wget https://ftpmirror.gnu.org/gcc/gcc-10.3.0/gcc-10.3.0.tar.gz
tar xvf gcc-10.3.0.tar.gz
```
#### 下载依赖的库
```shell
cd gcc-10.3.0
./contrib/download_prerequisties
```
#### 编译有两种，一种直接在源代码中编译，一种编译放在单独的目录，这里演示分开目录编译
```shell
cd ..
mkdir build
cd build
../gcc-10.3.0/configure -v --build=x86_64-linux-gnu --host=x86_64-linux-gnu --target=x86_64-linux-gnu --prefix=/opt/gcc-10.3.0 --enable-checking=release --enable-languages=c,c++,fortran --disable-multilib --program-suffix=-10.3
```
> 编译选项可以查询官网说明，这里以最常用的方式为例

#### 编译与安装
```shell
make -j
sudo make install-strip
```
### GCC多版本管理
版本管理利用update-alternatives工具，在linux上是一个比较好用的工具，下面以GCC举例说明，假如在机器上存在以下两个版本的GCC

```shell
/opt/gcc-9.3.0
/opt/gcc-10.3.0
```
#### 首先查看update-alternatives中已经注册的版本
```shell
sudo update-alternatives --display gcc
```
#### 利用update-alternatives注册新安装的尚未注册的多个版本（假设上面查询结果为空）
```shell
sudo update-alternatives --install /usr/bin/gcc gcc /opt/gcc-9.3.0/bin/gcc-9.3 50
sudo update-alternatives --install /usr/bin/gcc gcc /opt/gcc-10.3.0/bin/gcc-9.3 100
```
> 后面的数字表示该版本的优先级，当设置在auto模式时，优先级最高的版本生效

#### 配置版本
```shell
sudo update-alternatives --config gcc
```
> 可以选择特定的版本或者是auto模式

#### 删除某一版本，假如需要删除gcc-9.3的版本
```shell
sudo update-alternatives --remove gcc /opt/gcc-9.3.0/bin/gcc-9.3
```