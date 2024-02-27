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

![001](https://github.com/winfredzen/Linux-Learn/blob/main/shell/images/001.png)



将脚本移动到`/home/wz/bin`目录下，然后在`/home/wz`目录下，直接输入`hello_world`，就可以运行脚本了

![002](https://github.com/winfredzen/Linux-Learn/blob/main/shell/images/002.png)



**脚本文件放在哪里？**

> 如果我们编写了一个脚本，系统中的每个用户都可以使用它， 那么这个脚本的传统位置是 `/usr/local/bin`。系统管理员使用的脚本经常放到 `/usr/local/sbin` 目录下。 大多数情况下，本地支持的软件，不管是脚本还是编译过的程序，都应该放到 `/usr/local` 目录下， 而不是在 `/bin` 或 `/usr/bin` 目录下。这些目录都是由 Linux 文件系统层次结构标准指定，只包含由 Linux 发行商 所提供和维护的文件。





**文本输出方式**

参考：

+ [启动一个项目](http://billie66.github.io/TLCL/book/chap26.html)

1.使用多个`echo`输出

```shell
echo "<HTML>"
echo "      <HEAD>"
echo "            <TITLE>Page Title</TITLE>"
echo "      </HEAD>"
echo "      <BODY>"
echo "            Page body."
echo "      </BODY>"
echo "</HTML>"
```

2.使用一个`echo`输出

```shell
echo "<HTML>
    <HEAD>
          <TITLE>System Information Report</TITLE>
    </HEAD>
    <BODY>
          <H1>System Information Report</H1>
    </BODY>
</HTML>"
```

3.here document 或者 here script

一个 here document 是另外一种 I/O 重定向形式，我们 在脚本文件中嵌入正文文本，然后把它发送给一个命令的标准输入。它这样工作：

```shell
command << token
text
token
```

如下的例子：

```shell
#!/bin/bash
# Program to output a system information page
TITLE="System Information Report For $HOSTNAME"
CURRENT_TIME=$(date +"%x %r %Z")
TIME_STAMP="Generated $CURRENT_TIME, by $USER"
cat << _EOF_
<HTML>
         <HEAD>
                <TITLE>$TITLE</TITLE>
         </HEAD>
         <BODY>
                <H1>$TITLE</H1>
                <P>$TIME_STAMP</P>
         </BODY>
</HTML>
_EOF_
```

> 字符串`_EOF_`（意思是“文件结尾”， 一个常见用法）被选作为 `token`

**here documents 中的单引号和双引号会失去它们在 shell 中的特殊含义**

```shell
[me@linuxbox ~]$ foo="some text"
[me@linuxbox ~]$ cat << _EOF_
> $foo
> "$foo"
> '$foo'
> \$foo
> _EOF_
some text
"some text"
'some text'
$foo
```

> 正如我们所见到的，**shell 根本没有注意到引号**。**它把它们看作是普通的字符**。这就允许我们 在一个 here document 中可以随意的**嵌入引号**。对于我们的报告程序来说，这将是非常方便的。

> 开头的 “>” 字符是包含在 PS2shell 变量中的 shell 提示符。每当我们在 shell 中键入多行语句的时候， 这个提示符就会出现。现在这个功能有点儿晦涩，但随后，当我们介绍多行编程语句时，它会派上大用场。



> Here documents 可以和任意能接受标准输入的命令一块使用。在这个例子中，我们使用了 一个 here document 将一系列的命令传递到这个 ftp 程序中，为的是从一个远端 FTP 服务器中得到一个文件：
>
> ```shell
> #!/bin/bash
> # Script to retrieve a file via FTP
> FTP_SERVER=ftp.nl.debian.org
> FTP_PATH=/debian/dists/lenny/main/installer-i386/current/images/cdrom
> REMOTE_FILE=debian-cd_info.tar.gz
> ftp -n << _EOF_
> open $FTP_SERVER
> user anonymous me@linuxbox
> cd $FTP_PATH
> hash
> get $REMOTE_FILE
> bye
> _EOF_
> ls -l $REMOTE_FILE
> ```



如果我们把重定向操作符从 “`<<`” 改为 “`<<-`”，shell 会忽略在此 here document 中开头的 `tab` 字符。 这就能缩进一个 here document，从而提高脚本的可读性：

```shell
#!/bin/bash
# Script to retrieve a file via FTP
FTP_SERVER=ftp.nl.debian.org
FTP_PATH=/debian/dists/lenny/main/installer-i386/current/images/cdrom
REMOTE_FILE=debian-cd_info.tar.gz
ftp -n <<- _EOF_
    open $FTP_SERVER
    user anonymous me@linuxbox
    cd $FTP_PATH
    hash
    get $REMOTE_FILE
    bye
_EOF_
ls -l $REMOTE_FILE
```





















