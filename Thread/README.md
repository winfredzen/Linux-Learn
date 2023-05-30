# Posix线程

参考：

+ [Linux 高级编程- Posix 线程](https://dlonng.com/posts/thread)



`Posix Pthread`：POSIX 标准的拓展，可以提供用户级和内核级的库，但**仅仅是线程行为规范，而不是实现**，**Linux**，Solaris 等 OS 都实现了这个规范



**比较，获取线程 ID**

`pthread_t`类型表示一个线程id，该类型在Linux上一个无符号长整型

```c
#include <pthread.h>

// 比较 2 个线程 ID
int pthread_equal(pthread_t t1, pthread_t t2);

// 获取自身线程 ID
pthread_t pthread_self(void);
```



**创建线程**

Posix 线程定义下面的函数来创建新的线程：

```c
#include <pthread.h>

/*
 * thread: 指向线程 ID
 * attr: 定制线程属性，传递 NULL 设置默认属性
 * start_routine: 要运行的线程函数
 * arg: 要传递的函数参数
 * return: 成功返回 0，失败返回错误码，但不会设置 erron
 */
int pthread_create(pthread_t *thread, const pthread_attr_t *attr,
void *(*start_routine) (void *), void *arg);
```

如下的例子，在Mac上，使用Xcode：

```c
#include <stdio.h>
#include <pthread.h>
#include <sys/types.h>
#include <unistd.h>

/* print pid and tid. */
void print_id(const char *s) {
    printf("%s pid %lu, tid 0x%lx\n", s,
            (unsigned long)getpid(),
            (unsigned long)pthread_self());
}

/* thread fun */
void *thread_fun(void *arg) {
    print_id("new  thread: ");
    return NULL;
}

int main(void) {
    pthread_t tid;

    // create pthread
    if (pthread_create(&tid, NULL, thread_fun, NULL) != 0) {
        printf("thread create failed.\n");
        return -1;
    }

    print_id("main thread: ");
    // 等待子线程执行完，后面会用 pthread_join 代替
    sleep(1);
    return 0;
}
```

打印结果如下：

```c
main thread:  pid 1466, tid 0x1000dfdc0
new  thread:  pid 1466, tid 0x7000024a3000
```



**线程终止**

如果进程中的任意线程调用了`exit`、`_Exit`或者`exit`，那么整个进程就会终止。并且如果信号的默认动作是终止进程，那么收到该信号的进程也会终止。

单个线程可以通过3种方式退出，因此可以在不终止整个进程的情况下，停止它的控制流。

1. 线程可以简单地从启动例程中返回，返回值是线程的退出码。

2. 线程可以被同一进程中的其他线程取消

3. 线程调用`pthead_exit`



```c
#include <pthread.h>

void pthread_exit(void *retval);
```



**等待**

```c
#include <pthread.h>

/*
 * thread: 要等待的线程 ID
 * retval: 线程的退出状态
 * return: 成功返回 0，失败返回错误编号
 */
int pthread_join(pthread_t thread, void **retval);
```

**调用线程将一直阻塞，直到线程 ID 为 thread 的线程调用 `pthread_exit`，被取消或从启动例程中返回**。并且，进程中的其他线程可以通过调用 `pthread_join` 函数获得该线程的退出状态。其中 `retval` 有 2 种情况：

1. 如果不为 NULL，则拷贝线程的退出状态码到 `retval`
2. 如果目标线程被取消，`PTHREAD_CANCELED` 被放到 `retval` 中

下面结合上面这两个函数来看一个**例子**：主线程开启 2 个子线程，然后分别使用 `return` 返回和 `pthread_exit` 退出，最后在主线程中获取线程的返回码。

```c
#include <stdio.h>
#include <pthread.h>

void *thread_fun1(void *arg) {
    printf("thread 1 return.\n");
    return ((void *)1);
}

void *thread_fun2(void *arg) {
    printf("thread 2 exit.\n");
    pthread_exit((void *)2);
}

int main(void) {
    pthread_t tid1, tid2;
    void *ret_val = NULL;
    // create thread 1 and 2
    pthread_create(&tid1, NULL, thread_fun1, NULL);
    pthread_create(&tid2, NULL, thread_fun2, NULL);
    // wait thread 1
    pthread_join(tid1, &ret_val);
    printf("thread 1 exit code %ld\n", (long)ret_val);
    // wait thread 2
    pthread_join(tid2, &ret_val);
    printf("thread 2 exit code %ld\n", (long)ret_val);

    return 0;
}
```

控制台输出如下：

```c
thread 2 exit.
thread 1 return.
thread 1 exit code 1
thread 2 exit code 2
```





















































































