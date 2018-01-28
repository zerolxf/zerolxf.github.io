---
title: webbench学习
date: 2018-01-28 14:13:47
categories:
    - linux
    - webbench
tags: linux 开源
---

# 开源代码学习


无聊至极,学习一下一些简单的开源项目

### Webbench

> Webbench是一个在linux下使用的非常简单的网站压测工具。它使用fork()模拟多个客户端同时访问我们设定的URL，测试网站在压力下工作的性能，最多可以模拟3万个并发连接去测试网站的负载能力。Webbench使用C语言编写, 代码实在太简洁，源码加起来不到600行。 --by [林木][1]

<!-- more -- >

#### 命令行参数解析
要看懂代码,最好先学习下`getopt_long`等函数的用法,他是c语言中用来解析命令行参数
分析一个很全的学习[博客1][2]和[博客2][3]
使用 getopt_long_only 时, - 和 --都可以作用于长选项, 而使用 getopt_only 时, 只有 --可以作用于长选项.
短选项:就是只是一个字符串如`-a -v`,只用-表示
长选项:一般如`--help --version`,在getopt_long_only中`-name`也是

`const char * optstring` optstring用来指定可以处理哪些选项
`"hvx:"`该示例表明程序可以接受3个选项: -h -v -x, 其中 x 后面的 :表示该选项后面要跟一个参数, 即如 -x 10的形式, 选项后面跟的参数会被保存到 optarg 变量中. 下面是一个使用示例
`extern int optind`  下一次调用getopt的时，从optind存储的位置处重新开始检查选项。 

#### 字符串处理函数
`strrchr()` 函数用于查找某字符在字符串中最后一次出现的位置，其原型为：
    `char * strrchr(const char *str, int c);`
    
`atoi` (表示ascii to integer)是把字符串转换成整型数的一个函数

`strncasecmp()`用来比较参数s1 和s2 字符串前n个字符，比较时会自动忽略大小写的差异

`strstr(str1,str2)` 函数用于判断字符串str2是否是str1的子串。如果是，则该函数返回str2在str1中首次出现的地址；否则，返回NULL
`strchr`是计算机的一个函数，原型为extern char *strchr(const char *s,char c)，可以查找字符串s中首次出现字符c的位置

函数`char * index(const char *s, int c); `函数说明index()用来找出参数s 字符串中第一个出现的参数c 地址

### fork讲解
[fork讲解][5]
这个代码总共执行多少次？
``` c
#include <stdio.h>
int main(int argc, char* argv[])
{
   fork();
   fork() && fork() || fork();
   fork();
   printf("+/n");
}
```
**20!** fork返回值只有0，1
所以最终结果就是0/1***0/1,所以只需要分析中间3个合理组合，只有5个符合条件，所以5×4=20
### 源代码分析
这里有别人[源代码分析][4]

### 

[1]: https://www.zhihu.com/question/26469623
[2]: http://blog.zhangjikai.com/2016/03/05/%E3%80%90C%E3%80%91%E8%A7%A3%E6%9E%90%E5%91%BD%E4%BB%A4%E8%A1%8C%E5%8F%82%E6%95%B0--getopt%E5%92%8Cgetopt_long/
[3]: http://blog.csdn.net/men_wen/article/details/61934376
[4]: http://www.cnblogs.com/xuning/p/3888699.html
[5]: http://blog.csdn.net/jason314/article/details/5640969

