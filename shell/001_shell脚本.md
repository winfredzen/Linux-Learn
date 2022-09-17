# Shell脚本

参考：

+ [编写第一个 Shell 脚本](http://billie66.github.io/TLCL/book/chap25.html)



在桌面创建一个`hello_world.sh`

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

