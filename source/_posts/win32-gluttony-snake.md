---
title: 利用Win32Api实现的一个简单的贪食蛇游戏
tags:
  - C/C++
  - Win32Api
  - Windows程序设计
categories: C/C++
comments: true
abbrlink: WPj0K1s6sJd6LQqeogPUHg
date: 2016-07-14 16:21:11
---
这是大学时期写的代码，当时看[Windows程序设计(第5版)](https://www.amazon.cn/Windows%E7%A8%8B%E5%BA%8F%E8%AE%BE%E8%AE%A1-%E4%BD%A9%E6%8E%AA%E5%B0%94%E5%BE%B7/dp/B00426BTC6/ref=sr_1_2?ie=UTF8&qid=1468484710&sr=8-2&keywords=windows%E7%A8%8B%E5%BA%8F%E8%AE%BE%E8%AE%A1)学习利用Win32Api编程，当时还没毕业，课程讲到Winform编程，便想通过对Win32Api的学习多了解Winform的一些比较底层实现。
<!-- more -->
废话不多说，直接上代码：
{% include_code 贪食蛇游戏 lang:C++ cpp/gluttonysnake.cpp %}
代码在VS2010下编译运行(*我使用的开发环境，VS其它版本也可以*)，可见游戏界面如下：
{% asset_img  1-1.PNG 贪食蛇游戏界面 %}
&ensp;
以上代码未考虑通关情况，也就是如果水平够高蛇会把格子占满，然后Game over。
**注:** 若在编译时报错：
```
error LNK2019: 无法解析的外部符号 _main，该符号在函数 ___tmainCRTStart
```
可按以下方法解决：
- 右键点击-->项目，选择-->属性，属性页 窗口弹出；
- 接着，点击连接器->系统，在右侧会看到“子系统”选项，将选项改为**/subsystem:windows**.

最后附上Github项目地址：*[gluttonysnake](https://github.com/hulala1021/gluttonysnake)*
{% githubCard hulala1021 gluttonysnake %}
