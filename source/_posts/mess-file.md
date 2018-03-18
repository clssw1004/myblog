---
title: 文件混淆工具
date: 2016-07-22 22:17:00
tags:
  - C#
  - 生产力
categories: C#
---

## 功能一：混淆文件（目前仅实现该功能）
  我们在上传文件到某网盘时，有时会因为文件内容违规，不让上传，或者是以前上传的文件，但现在不让下载了；

  本工具作用是打乱文件字节顺序，与文件名，让网盘无法识别文件内容，以实现正常的上传下载。转换后文件组成部分为：文件名4k + 文件数据。
<!--more-->
**注:该工具一定意义上有加密作用，但不建议依赖其功能做加密用**
### API
  - **本程序由C#实现**
  - **打乱或恢复文件顺序**
  ```c#
  FileMess.Mess(fileName, MessOption,delFile=false);
  ```
  fileName为文件全路径；
  MessOption为枚举类型：

  ```c#
  MessOption.MESS  //打乱顺序
  MessOption.UNMESS //恢复
  ```
  delFile是一个布尔值，true为处理后删除源文件，false为保留源文件

  - **整个文件夹操作**
  ```c#
  FileMess.MessDir(dirName, MessOption.MESS, delFile);
  ```
  dirName为文件夹全路径，后面两个参数同上

## 窗口工具使用
如果你没有C#开发并想要直接使用本工具，这里提供了编译好的二进制运行文件，本工具界面使用Winform开发：
- 下载地址 **[https://github.com/hulala1021/file-tool/releases](https://github.com/hulala1021/file-tool/releases)**

- 运行后界面如下：
{% asset_img  mess-tool-gui.PNG 界面 %}
可见界面中已经封装好以上API的操作了，可直接点击运行使用；

## 常见问题
- **问题：**如果我不小心把一个文件打乱两次怎么办：
**解决方案：**这个很简单，进行两次恢复操作就可以了。

## Github项目地址
[https://github.com/hulala1021/file-tool](https://github.com/hulala1021/file-tool)
&ensp;
{% githubCard hulala1021 file-tool %}
&ensp;





