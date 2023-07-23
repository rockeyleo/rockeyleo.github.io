---
title: csp-noip备战日志1-小寄巧
date: 2023-07-21 21:22:22
tags: [csp-noip备战日志,OI,学习手记]
---

## 常数优化

待续

(数据结构篇同样有许多常数优化技巧。

## 对拍

对拍需要四个程序，一个可能正确的待测代码，一个保对的暴力，一个数据生成器，一个文件比对程序

鉴于NOIP山东赛区并不提供NOI linux，也就完全有可能不支持python，所以建议使用batch编写对拍程序。

不过，对拍并非万能（~~比如你首先要会写暴力~~），需要权衡收益和时间开销，可能产生误判，且较难应对spj，所以要适度适时使用

#### 1. 数据生成器

#### 2. 利用fc命令编写对拍器

对拍主要利用cmd的`fc`命令（在linux下为diff），这个命令可以比较文本文件的差异。我们可以利用这个命令快速对比标程和待测代码输出有无差异。配合上数据生成器和文件流，就可以自动化地完成多组对拍。

所以这个对拍程序的基本架构是：

```
循环：
	运行数据生成器->data.in
	data.in->暴力->baoli.out
	date.in->待测->test.out
	fc baoli.out test.out检测返回值，不正确时跳出
```

这个对拍程序可以使用C++完成。

```C++
#include <bits/stdc++.h>
//如果你不习惯使用万能头，system()位于<cstdlib>中
using namespace std;
int main(){
	while(1){
		system("data.exe");		//system()中的参数（注意是字符串类型）
        						//相当于直接在cmd中输入这个命令
        						//data.exe每运行一次，data.in的数据都会被刷新，下面同理
        system("baoli.exe");	//可能需要提前在ide中编译一下
        system("test.exe");
        if(system("fc test.out baoli.out")){
            break;	//当fc比对不一致时，会返回参数1。这个返回值会被system函数捕获
        }
    }
}
```

这样一来，当某个数据验证不成功的时候，程序会立刻退出，这时打开data.in，其中就是没有通过的数据，可以重复测试。如果程序一直没有退出，那就是一直成功的状态。




## 还是复杂度分析

![](https://i.loli.net/2019/10/21/ipIwRUbVNxaZl6J.jpg)


## 输入输出优化

[【洛谷日报#110】浅谈C++ IO优化——读优输优方法集锦 ](https://zhuanlan.zhihu.com/p/55304700)

### 1. 读入优化（快读）

先扔个常用模板

~~绝大部分时候你只需要（可能也只能）记住这个玩意~~

```C++
inline int read() {
    int ret=0,f=1;char ch=getchar();
    while (ch<'0'||ch>'9') {if (ch=='-') f=-f;ch=getchar();}
    while (ch>='0'&&ch<='9') ret=ret*10+ch-'0',ch=getchar();
    return ret*f;
}
```

使用时直接`int a = read();`即可。

我们来分析一下代码：

- 核心就是`getchar()`非常快

- `getchar()`仅用char的方式读取一个字符，所以需要用一个ret进行累加

- 用f来控制输入正负。以及显然，需要保证读入的数字的格式是合法的

- 如果题目的读入数据需要开`longlong`，那么**快读也需要开`longlong`**

  ~~十年OI一场空，不开`longlong`见祖宗~~

- 切记切记，**即使你能保证数据均为正，也必须要写一个`while (ch<'0'||ch>'9') {ch=getchar();}`在上面！！** 否则可能会得到意料之外的输入（比如说换行符）。

- 和使用stdio一样，**用快读不要关同步！！** 不然可能得不到正确的结果[或会T飞](https://www.luogu.com.cn/record/88253964)

### 2. 输出优化

一般输出规模不会至于用这个玩意，且仅利用`putchar()`的输出优化甚至比`printf()`还要慢，但你仍然可能会在使用`__int128_t`的时候使用它来进行输出。所以这里直接放一个int128版本的：

```C++
void write(__int128_t x){
    __int128_t y=10,len=1;
    while(y<=x){y*=10;len++;}
    while(len--){y/=10;putchar(x/y+48);x%=y;}
}
// y和x的类型都要是__int128_t，不然会出问题
```


upd：[~~(这两天看到一个超级快读的离谱程序，放过来镇个楼)~~](https://www.luogu.com.cn/paste/ilt13pl4)



## 算符优先级

这玩意也算是个大坑……

C++运算符顺序表：

![](https://img-blog.csdnimg.cn/9e6d8a9983e5430081e74994eddeafe3.png#pic_center)

注意位运算、类型转换的作用范围，以及异或不要忘记加括号。


## 有关C++STL
