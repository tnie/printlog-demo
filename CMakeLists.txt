# CMake 最低版本号要求
cmake_minimum_required (VERSION 2.8)
# 项目信息
project (demo)
# 查找当前目录下的所有源文件
aux_source_directory(. DIR_LIB_SRCS)
message(STATUS "DIR_LIB_SRCS=${DIR_LIB_SRCS}")

## 方式一 添加 pringlog 子目录
## 方式二 显式指定头文件以及库文件。缺乏灵活性，不专业
## 当第三方库项目变得负责时，或者头文件在 n 级目录之后
include_directories(printlog-library)                   # 相对源码路径（CMakeLists.txt 路径）
link_directories(../printlog-library/build/Debug)       # 相对执行命令的路径（build 目录） why?
## 方式三 使用 ExternalProject_Add 特性

# 指定生成目标
add_executable(demo ${DIR_LIB_SRCS})
# 添加链接库
target_link_libraries(demo printlog)