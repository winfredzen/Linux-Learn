# read

参考：

+ [读取键盘输入](http://billie66.github.io/TLCL/book/chap29.html)

`read`用来从标准输入读取数值

```shell
read [-options] [variable...]
```

1.用来读取键盘输入

2.当使用重定向的时候，读取文件中的一行数据

如下的例子，读取一个输入：

```shell
#!/bin/bash
# read-integer: evaluate the value of an integer.
echo -n "Please enter an integer -> "
read int
if [[ "$int" =~ ^-?[0-9]+$ ]]; then
    if [ $int -eq 0 ]; then
        echo "$int is zero."
    else
        if [ $int -lt 0 ]; then
            echo "$int is negative."
        else
            echo "$int is positive."
        fi
        if [ $((int % 2)) -eq 0 ]; then
            echo "$int is even."
        else
            echo "$int is odd."
        fi
    fi
else
    echo "Input value is not an integer." >&2
    exit 1
fi
```

> `echo -n "Please enter an integer -> "` `-n` 表示删除输出结果末尾的换行符



`read` 可以给多个变量赋值

```shell
#!/bin/bash
# read-multiple: read multiple values from keyboard
echo -n "Enter one or more values > "
read var1 var2 var3 var4 var5
echo "var1 = '$var1'"
echo "var2 = '$var2'"
echo "var3 = '$var3'"
echo "var4 = '$var4'"
echo "var5 = '$var5'"
```

```shell
[me@linuxbox ~]$ read-multiple
Enter one or more values > a b c d e
var1 = 'a'
var2 = 'b'
var3 = 'c'
var4 = 'd'
var5 = 'e'
[me@linuxbox ~]$ read-multiple
Enter one or more values > a
var1 = 'a'
var2 = ''
var3 = ''
var4 = ''
var5 = ''
[me@linuxbox ~]$ read-multiple
Enter one or more values > a b c d e f g
var1 = 'a'
var2 = 'b'
var3 = 'c'
var4 = 'd'
var5 = 'e f g'
```

如果 `read` 命令之后没有列出变量名，则一个 shell 变量，`REPLY`，将会包含 所有的输入：

```shell
#!/bin/bash
# read-single: read multiple values into default variable
echo -n "Enter one or more values > "
read
echo "REPLY = '$REPLY'"
```

```shell
[me@linuxbox ~]$ read-single
Enter one or more values > a b c d
REPLY = 'a b c d'
```



`read` 支持以下选项：

| 选项         | 说明                                                         |
| :----------- | :----------------------------------------------------------- |
| -a array     | 把输入赋值到数组 array 中，从索引号零开始。我们 将在第36章中讨论数组问题。 |
| -d delimiter | 用字符串 delimiter 中的第一个字符指示输入结束，而不是一个换行符。 |
| -e           | 使用 Readline 来处理输入。这使得与命令行相同的方式编辑输入。 |
| -n num       | 读取 num 个输入字符，而不是整行。                            |
| -p prompt    | 为输入显示提示信息，使用字符串 prompt。                      |
| -r           | Raw mode. 不把反斜杠字符解释为转义字符。                     |
| -s           | Silent mode. 不会在屏幕上显示输入的字符。当输入密码和其它确认信息的时候，这会很有帮助。 |
| -t seconds   | 超时. 几秒钟后终止输入。若输入超时，read 会返回一个非零退出状态。 |
| -u fd        | 使用文件描述符 fd 中的输入，而不是标准输入。                 |



例如，通过`-p` 选项，我们能够提供提示信息：

```shell
#!/bin/bash
# read-single: read multiple values into default variable
read -p "Enter one or more values > "
echo "REPLY = '$REPLY'"
```



通过 `-t` 和 `-s` 选项，我们可以编写一个这样的脚本，读取“**秘密**”输入，并且如果在特定的时间内 输入没有完成，就终止输入

```shell
#!/bin/bash
# read-secret: input a secret pass phrase
if read -t 10 -sp "Enter secret pass phrase > " secret_pass; then
    echo "\nSecret pass phrase = '$secret_pass'"
else
    echo "\nInput timed out" >&2
    exit 1
fi
```



## 菜单

呈现给用户一系列选择，并要求用户选择一项

如下的例子：

```shell
#!/bin/bash
# read-menu: a menu driven system information program
clear
echo "
Please Select:

    1. Display System Information
    2. Display Disk Space
    3. Display Home Space Utilization
    0. Quit
"
read -p "Enter selection [0-3] > "

if [[ $REPLY =~ ^[0-3]$ ]]; then
    if [[ $REPLY == 0 ]]; then
        echo "Program terminated."
        exit
    fi
    if [[ $REPLY == 1 ]]; then
        echo "Hostname: $HOSTNAME"
        uptime
        exit
    fi
    if [[ $REPLY == 2 ]]; then
        df -h
        exit
    fi
    if [[ $REPLY == 3 ]]; then
        if [[ $(id -u) -eq 0 ]]; then
            echo "Home Space Utilization (All Users)"
            du -sh /home/*
        else
            echo "Home Space Utilization ($USER)"
            du -sh $HOME
        fi
        exit
    fi
else
    echo "Invalid entry." >&2
    exit 1
fi
```

























