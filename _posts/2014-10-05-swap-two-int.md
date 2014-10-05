---
layout: post
category: algorithm
title: swap两个int指针指向的数
tagline: by hombo
tags: [algorithm,point,c]
---
这是一道最近的面试题，感谢斌哥的奉献！
题目是： 填写下面的函数，实现a和b指针指向的两个数的交换。

    void swap(int *a , int *b){
        *a = ________ ;
        *b = ________ ;
        *a = ________ ;
    }

<!--more-->

##分析:##

我们知道对于两个数a,b的交换，如果不借助其他变量，也是可以实现的，方法有下面几种：

* 用加减法:

利用加减法的 `交换律` 和 `结合律`

    void swap(int a , int b){
        a = a + b;
        b = a - b; //b = a + b - b = a 
        a = a - b; //a = a + b - a = b
    }

* 用乘除法:

注意，这个方法需要满足 `a*b != 0` 

    void swap(int a , int b){
        a = a * b;
        b = a / b; //b = a * b / b = a 
        a = a / b; //a = a * b / a = b
    }

其实这个方法用到了乘除法的 `分配率`

 * 用异或运算：

利用异或的性质: `交换律` , `结合律`  , `a ^ a = 0` , `0 ^ a = a` 

    void swap(int a , int b){
        a = a ^ b;
        b = a ^ b; //b = a ^ b ^ b = a ^ 0 = a 
        a = a ^ b; //a = a ^ b ^ a = a ^ a ^ b = 0 ^ b = b
    }

而本题给的是指针，而且等式左边都是指针，也就是说没办法直接改变指针指向的内存位置的值，那怎么办呢？

##解决方法:##

虽然等式左边没有给出[左值](http://baike.baidu.com/view/836474.htm?fr=aladdin),但我们可以在等式右边加入左值，也就是说可以写成这种形式：

    *a = *a + *b

为了不改变指针的值，还需要用点小技巧，就是用“,”来分割语句，可以写成下面这种形式：

    a = (*a = *a + *b , a )

这是因为用“,”分割的语句，语句的返回值是最后一个子语句，这里就是a。现在应该明白了吧！那本题的一种解法如下：

    void swap(int *a , int *b){
        a = (*a = *a + *b , a);
        b = (*b = *a - *b , b); //*b = *a + *b - *b = *a 
        a = (*a = *a - *b , a); //*a = *a + *b - *a = *b
    }

也可以用乘除法和异或运算来实现，大同小异，这里不再给出。