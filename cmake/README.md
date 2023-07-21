# cmake学习笔记

B站的视频教程不错：

+ [CMake 保姆级教程【C/C++】](https://www.bilibili.com/video/BV14s4y1g7Zj/?p=2&spm_id_from=pageDriver&vd_source=308fc9b57cdc925a463da02262234ff6)

其对应的文档教程为：

+ [CMake 保姆级教程（上）](https://subingwen.cn/cmake/CMake-primer/index.html)
+ [CMake 保姆级教程（下）](https://subingwen.cn/cmake/CMake-advanced/)



## 注释

使用 `#[[ ]]` 形式进行块注释



## cmake_minimum_required

`cmake_minimum_required`指定使用的 `cmake` 的最低版本

```cmake
cmake_minimum_required(VERSION 3.24)
```





## project

`project`定义工程名称，并可指定工程的版本、工程描述、web主页地址、支持的语言（默认情况支持所有语言），如果不需要这些都是可以忽略的，只需要指定出工程名字即可

```cmake
project(CMakeTest C)
```



## add_executable

`add_executable`定义工程会生成一个可执行程序

```cmake
add_executable(可执行程序名 源文件名称)
```

这里的可执行程序名和`project`中的项目名没有任何关系

源文件名可以是一个也可以是多个，如有多个可用空格或`;`间隔

```cmake
# 样式1
add_executable(app add.c div.c main.c mult.c sub.c)
# 样式2
add_executable(app add.c;div.c;main.c;mult.c;sub.c)
```



## set

### 1.定义变量

```cmake
# SET 指令的语法是：
# [] 中的参数为可选项, 如不需要可以不写
SET(VAR [VALUE] [CACHE TYPE DOCSTRING [FORCE]])
```

+ `VAR`：变量名

+ `VALUE`：变量值

如：

```cmake
# 方式1: 各个源文件之间使用空格间隔
# set(SRC_LIST add.c  div.c   main.c  mult.c  sub.c)

# 方式2: 各个源文件之间使用分号 ; 间隔
set(SRC_LIST add.c;div.c;main.c;mult.c;sub.c)
add_executable(app  ${SRC_LIST})
```



### 2.指定使用的C++标准

C++标准对应有一宏叫做`DCMAKE_CXX_STANDARD`

在CMake中想要指定C++标准有两种方式

1.在 `CMakeLists.txt` 中通过 `set` 命令指定

```cmake
#增加-std=c++11
set(CMAKE_CXX_STANDARD 11)
#增加-std=c++14
set(CMAKE_CXX_STANDARD 14)
#增加-std=c++17
set(CMAKE_CXX_STANDARD 17)
```



2.在执行 cmake 命令的时候指定出这个宏的值

```cmake
#增加-std=c++11
cmake CMakeLists.txt文件路径 -DCMAKE_CXX_STANDARD=11
#增加-std=c++14
cmake CMakeLists.txt文件路径 -DCMAKE_CXX_STANDARD=14
#增加-std=c++17
cmake CMakeLists.txt文件路径 -DCMAKE_CXX_STANDARD=17
```



### 3.指定输出的路径

`EXECUTABLE_OUTPUT_PATH`宏表示输出的路径

```cmake
set(HOME /home/robin/Linux/Sort)
set(EXECUTABLE_OUTPUT_PATH ${HOME}/bin)
```

+ 第一行：定义一个变量用于存储一个绝对路径
+ 第二行：将拼接好的路径值设置给`EXECUTABLE_OUTPUT_PATH`宏
+ 如果这个路径中的子目录不存在，会自动生成，无需自己手动创建



## 搜索文件

> `PROJECT_SOURCE_DIR`为包含`PROJECT()`的最近一个`CMakeLists.txt`文件所在的文件夹
>
> 参考：
>
> + [cmake中PROJECT_SOURCE_DIR的遐思](https://blog.csdn.net/wangxiao7474/article/details/112967433)





> `CMAKE_CURRENT_SOURCE_DIR`指的是当前处理的 `CMakeLists.txt` 所在的路



如果不确定的话，可输出，如：

```cmake
message("PROJECT_SOURCE_DIR: ${PROJECT_SOURCE_DIR}")
```



如果一个项目里边的源文件很多，在编写`CMakeLists.txt`文件的时候不可能将项目目录的各个文件一一罗列出来，这样太麻烦也不现实。所以，在`CMake`中为我们提供了搜索文件的命令，可以使用`aux_source_directory`命令或者`file`命令。



### 1.aux_source_directory

```cmake
aux_source_directory(< dir > < variable >)
```

+ `dir`：要搜索的目录

+ `variable`：将从dir目录下搜索到的源文件列表存储到该变量中



如：

```cmake
# 搜索 src 目录下的源文件
aux_source_directory(${CMAKE_CURRENT_SOURCE_DIR}/src SRC_LIST)
add_executable(app  ${SRC_LIST})
```



### 2.file

```cmake
file(GLOB/GLOB_RECURSE 变量名 要搜索的文件路径和文件类型)
```

+ `GLOB`: 将指定目录下搜索到的满足条件的所有文件名生成一个列表，并将其存储到变量中。
+ `GLOB_RECURSE`：递归搜索指定目录，将搜索到的满足条件的文件名生成一个列表，并将其存储到变量中。

搜索当前目录的src目录下所有的源文件，并存储到变量中

```cmake
file(GLOB MAIN_SRC ${CMAKE_CURRENT_SOURCE_DIR}/src/*.cpp)
file(GLOB MAIN_HEAD ${CMAKE_CURRENT_SOURCE_DIR}/include/*.h)
```

































