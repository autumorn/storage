---
title: LLVM学习系列(一)
description: LLVM编译与开发环境配置
date: 2022-04-25
categories:
    - 编译
tags:
    - LLVM
---

### LLVM下载与编译
参考链接[LLVM编译](https://github.com/llvm/llvm-project#getting-the-source-code-and-building-llvm)进行，下面演示常用流程
#### 1. 下载LLVM源代码
```shell
git clone https://github.com/llvm/llvm-project.git
```
#### 2. 配置LLVM编译选项
```shell
cd llvm-project

cmake -S llvm -B build -G "Unix Makefiles" -DLLVM_ENABLE_PROJECTS="clang;lldb;compiler-rt" -DCMAKE_INSTALL_PREFIX=/home/xxx/llvm14
```
#### 3. 编译LLVM
```shell
cd build
make -j
make install
```

### LLVM开发环境配置
#### 1. 将编译出来的LLVM添加到PATH，这样可以使用llvm编译出来的各种工具
```shell
export PATH=/home/xxx/llvm14/bin:$PATH
export LD_LIBRARY_PATH=/home/xxx/llvm14/lib:$LD_LIBRARY_PATH
```
> 因为llvm的链接器lld仍然在开发中,需要使用gcc的链接器ld,所以如果使用clang进行编译的话只能编译到目标文件,最后将目标文件链接起来使用gcc的工具

#### 2. 创建项目，以如下代码作为示例：
```c++
#include <iostream>
#include "llvm/ADT/SmallVector.h"

int main() {
    llvm::SmallVector<int, 10> s_vec;
    s_vec.push_back(10);
    s_vec.push_back(20);
    std::cout << "sum is " << s_vec[0] + s_vec[1] << std::endl;
}
```
编写项目CMakeLists.txt文件
```cmake
cmake_minimum_required(VERSION 3.10)

project(main)
#set(LLVM_DIR /home/xxx/llvm14/lib/cmake/llvm) (if can't find llvm set)
find_package(LLVM 14.0 REQUIRED CONFIG)

if (NOT LLVM_FOUND)
    message(FATAL_ERROR "LLVM NOT FOUND")
endif()

include_directories(${LLVM_INCLUDE_DIRS})
add_definitions(${LLVM_DEFINITIONS})

llvm_map_components_to_libnames(llvm_libs bitreader core support)

add_executable(main main.cc)
target_link_libraries(main ${llvm_libs})
```
> 如果LLVM使用find_package无法找到，需要设置LLVM_DIR变量，将LLVM编译后的cmake module查找路径加入cmake module 搜索路径，并且使用CONFIG的导入模式

> llvm_map_components_to_libnames是比较老的映射库名称接口，可以使用新的llvm-config命令来查找需要使用的参数，比如查询所有的llvm库名称，使用命令llvm-config --components即可，将需要的库填入map函数

#### 3. 编译运行
