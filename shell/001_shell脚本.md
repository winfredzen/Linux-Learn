# Shell脚本

参考：

+ [编写第一个 Shell 脚本](http://billie66.github.io/TLCL/book/chap25.html)



在桌面创建一个`hello_world`（注意不需要后缀`.sh`）

```shell
#!/bin/bash

# This is our first script.

echo 'Hello world!';
```

> `#!`字符序列是一种特殊的结构叫做 `shebang`。 这个 `shebang` 被用来告诉操作系统将执行此脚本所用的**解释器**的名字。每个 shell 脚本都应该把这一文本行作为它的第一行。

使用`ls -l hello_world.sh`查看可执行权限

```sh
-rw-rw-r-- 1 wz wz 63  9月 17 09:45 hello_world.sh
```

`chmod 755 hello_world.sh` 修改文件为可执行

```shell
-rwxr-xr-x 1 wz wz 63  9月 17 09:45 hello_world.sh
```



**执行脚本**

```shell
wz@wz-virtual-machine:~/Desktop$ ./hello_world.sh 
Hello world!
```

要执行脚本，需明确文件的路径。如果没有指定，如下面的情形所示：

```shell
wz@wz-virtual-machine:~/Desktop$ hello_world
Command 'hello_world' not found, did you mean:
  command 'hello-world' from snap hello-world (6.4)
See 'snap info <snapname>' for additional versions.
```

> 回顾一下，如果没有给出可执行程序的明确路径名，那么系统每次都会 搜索一系列的目录，来查找此可执行程序。这个`/bin` 目录就是其中一个系统会自动搜索的目录。 这个目录列表被存储在一个名为 `PATH` 的环境变量中。这个 `PATH` 变量包含一个由冒号分隔开的目录列表。 我们可以查看 `PATH` 的内容
>
> ```shell
> wz@wz-virtual-machine:~/Desktop$ echo $PATH
> /usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:/snap/bin
> ```



把`/home/wz/bin`加入到PATH中，编辑`.bashrc`文件，加入：

```shell
export PATH=~/bin:"$PATH"
```

然后使用`source ~/.bashrc` ，也可以使用`. .bashrc`

> 这个点（`.`）命令是 `source` 命令的同义词，一个 shell 内建命令，用来读取一个指定的 shell 命令文件， 并把它看作是从键盘中输入的一样。





























