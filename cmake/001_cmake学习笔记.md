# cmake学习笔记

参考如下的文档：

+ [CMake 保姆级教程（上）](https://subingwen.cn/cmake/CMake-primer/index.html)
+ [CMake 保姆级教程（下）](https://subingwen.cn/cmake/CMake-advanced/)



> CMake看成一款自动生成 Makefile的工具，其编译流程如下图：
>
> ![001](./images/001.png)





本人在MacOS上，使用CLion IDE编写代码测试

cmake之后再make，会多出许多新的文件

![002](./images/002.png)





这样会导致整个项目目录看起来很混乱，不太容易管理和维护

可以把生成的这些与项目源码无关的文件统一放到一个对应的目录里边

```shell
mkdir build
cd ./build
cmake ..
make
```

![003](./images/003.png)



## 目录变更后

修改目录，将`*.c`文件移入src，将`*.h`文件移动到include目录下

此时make时就会有如下的提示：

![004](./images/004.png)

原因是：头文件搜索是从当前目录下找的，而在src目录下是没有这个头文件的，头文件是在include下的

可指定头文件路径:

```cmake
# 指定头文件路径
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/include)
```









































