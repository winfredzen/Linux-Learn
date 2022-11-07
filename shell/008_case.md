# case

参考：

+ [流程控制：case 分支](http://billie66.github.io/TLCL/book/chap32.html)



在上一节使用`if`创建的menu菜单选择，也可以通过`case`来实现

`case`的语法规则如下：

```shell
case word in
    [pattern [| pattern]...) commands ;;]...
esac
```

实现`menu`菜单的选择：

```shell
#!/bin/bash
# case-menu: a menu driven system information program
clear
echo "
Please Select:
1. Display System Information
2. Display Disk Space
3. Display Home Space Utilization
0. Quit
"
read -p "Enter selection [0-3] > "
case $REPLY in
    0)  echo "Program terminated."
        exit
        ;;
    1)  echo "Hostname: $HOSTNAME"
        uptime
        ;;
    2)  df -h
        ;;
    3)  if [[ $(id -u) -eq 0 ]]; then
            echo "Home Space Utilization (All Users)"
            du -sh /home/*
        else
            echo "Home Space Utilization ($USER)"
            du -sh $HOME
        fi
        ;;
    *)  echo "Invalid entry" >&2
        exit 1
        ;;
esac
```

![018](https://github.com/winfredzen/Linux-Learn/blob/main/shell/images/018.png)

> `case` 命令检查一个变量值，在我们这个例子中，就是 `REPLY` 变量的变量值，然后试图去匹配其中一个具体的模式。 当与之相匹配的模式找到之后，就会执行与该模式相关联的命令。若找到一个模式之后，就不会再继续寻找。



## 模式

`case` 语句使用的模式和路径展开中使用的那些是一样的。模式以一个 “`)`” 为终止符

| 模式         | 描述                                                         |
| :----------- | :----------------------------------------------------------- |
| a)           | 若单词为 “a”，则匹配                                         |
| [[:alpha:]]) | 若单词是一个字母字符，则匹配                                 |
| ???)         | 若单词只有3个字符，则匹配                                    |
| *.txt)       | 若单词以 “.txt” 字符结尾，则匹配                             |
| *)           | 匹配任意单词。把这个模式做为 case 命令的最后一个模式，是一个很好的做法， 可以捕捉到任意一个与先前模式不匹配的数值；也就是说，捕捉到任何可能的无效值。 |



如：

```shell
#!/bin/bash
read -p "enter word > "
case $REPLY in
    [[:alpha:]])        echo "is a single alphabetic character." ;;
    [ABC][0-9])         echo "is A, B, or C followed by a digit." ;;
    ???)                echo "is three characters long." ;;
    *.txt)              echo "is a word ending in '.txt'" ;;
    *)                  echo "is something else." ;;
esac
```

![019](https://github.com/winfredzen/Linux-Learn/blob/main/shell/images/019.png)



还可以使用竖线字符作为分隔符，把多个模式结合起来。这就创建了一个 “**或**” 条件模式。**这对于处理诸如大小写字符很有用处**。例如：

```shell
#!/bin/bash
# case-menu: a menu driven system information program
clear
echo "
Please Select:
A. Display System Information
B. Display Disk Space
C. Display Home Space Utilization
Q. Quit
"
read -p "Enter selection [A, B, C or Q] > "
case $REPLY in
q|Q) echo "Program terminated."
     exit
     ;;
a|A) echo "Hostname: $HOSTNAME"
     uptime
     ;;
b|B) df -h
     ;;
c|C) if [[ $(id -u) -eq 0 ]]; then
         echo "Home Space Utilization (All Users)"
         du -sh /home/*
     else
         echo "Home Space Utilization ($USER)"
         du -sh $HOME
     fi
     ;;
*)   echo "Invalid entry" >&2
     exit 1
     ;;
esac
```



### 执行多个动作

早于版本号4.0的 bash，case 语法只允许执行与一个成功匹配的模式相关联的动作。 匹配成功之后，命令将会终止。这里我们看一个测试一个字符的脚本：

```shell
#!/bin/bash
# case4-1: test a character
read -n 1 -p "Type a character > "
echo
case $REPLY in
    [[:upper:]])    echo "'$REPLY' is upper case." ;;
    [[:lower:]])    echo "'$REPLY' is lower case." ;;
    [[:alpha:]])    echo "'$REPLY' is alphabetic." ;;
    [[:digit:]])    echo "'$REPLY' is a digit." ;;
    [[:graph:]])    echo "'$REPLY' is a visible character." ;;
    [[:punct:]])    echo "'$REPLY' is a punctuation symbol." ;;
    [[:space:]])    echo "'$REPLY' is a whitespace character." ;;
    [[:xdigit:]])   echo "'$REPLY' is a hexadecimal digit." ;;
esac
```

```shell
[me@linuxbox ~]$ case4-1
Type a character > a
'a' is lower case.
```

> a既是小写，也是字母等



早于4.0的 bash，对于 case 语法绝不能匹配 多个测试条件。现在的 bash 版本，添加 “`;;&`” 表达式来终止每个行动，所以现在我们可以做到这一点：



```shell
#!/bin/bash
# case4-2: test a character
read -n 1 -p "Type a character > "
echo
case $REPLY in
    [[:upper:]])    echo "'$REPLY' is upper case." ;;&
    [[:lower:]])    echo "'$REPLY' is lower case." ;;&
    [[:alpha:]])    echo "'$REPLY' is alphabetic." ;;&
    [[:digit:]])    echo "'$REPLY' is a digit." ;;&
    [[:graph:]])    echo "'$REPLY' is a visible character." ;;&
    [[:punct:]])    echo "'$REPLY' is a punctuation symbol." ;;&
    [[:space:]])    echo "'$REPLY' is a whitespace character." ;;&
    [[:xdigit:]])   echo "'$REPLY' is a hexadecimal digit." ;;&
esac
```

![020](https://github.com/winfredzen/Linux-Learn/blob/main/shell/images/020.png)



> 添加的 “`;;&`” 的语法允许 `case` 语句继续执行下一条测试，而不是简单地终止运行。



























