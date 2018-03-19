---
title: 程序员生产力工具推荐
tags:
  - 生产力
  - 程序员
categories:
  - 生产力
date: 2016-07-20 22:43:47
---

**本文持续更新**

## Markdown 写文档利器
{% blockquote @引用来源 https://zh.wikipedia.org/wiki/Markdown %}
*Markdown*是一种轻量级标记语言，创始人为约翰·格鲁伯（John Gruber）。它允许人们“使用易读易写的纯文本格式编写文档，然后转换成有效的XHTML(或者HTML)文档”。[4]这种语言吸收了很多在电子邮件中已有的纯文本标记的特性。
{% endblockquote %}
<!-- more -->
- Markdown语法说明：https://zh.wikipedia.org/wiki/Markdown

## plantuml 画流程图、类图
*PlantUML*是一个用来绘制UML图的Java类库。支持的UML图包括：时序图、用例图、类图、组件图、活动图。
- example:
  ```plantuml
  @startuml
  Object <|-- ArrayList

  Object : equals()
  ArrayList : Object[] elementData
  ArrayList : size()
  @enduml
    ```
  {% plantuml %}
  Object <|-- ArrayList

  Object : equals()
  ArrayList : Object[] elementData
  ArrayList : size()
  {% endplantuml %}
  ```plantuml
  @startuml
  start
  if (Graphviz installed?) then (yes)
  :process all\ndiagrams;
  else (no)
  :process only
  __sequence__ and __activity__ diagrams;
  endif
  stop
  @enduml
  ```
  {% plantuml %}
  start
  if (Graphviz installed?) then (yes)
  :process all\ndiagrams;
  else (no)
  :process only
  __sequence__ and __activity__ diagrams;
  endif
  stop
  {% endplantuml %}

- plantuml语法说明：http://plantuml.com/sequence.html#Basic_examples

## ditto 剪切板历史管理
*ditto* : Windows下管理剪切板历史的小而强大的工具([官网](http://ditto-cp.sourceforge.net/))；