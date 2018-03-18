---
title: JAVA多线程编程——生产者消费者模式(2)
tags:
  - Java
  - 多线程编程
  - 生产者消费者问题
comments: true
categories: Java
---
**阅读本文前，确认你已看过{% post_link java-multithreading-1 JAVA多线程编程——生产者消费者模式(1) %}**。
本文实在上篇基础上对上篇已实现的代码进行扩展（功能性、适用性）及优化（结构、性能）；
<!--more-->
## 扩展
### 数据校验
&ensp;
{% asset_img  1-2.PNG 流水线 %}
**需求：**已知有4个操作(*打磨、抛光、焊接、质检*)，现需要在个操作之前现检验上一步操作是否符合要求，否则无法进行下一步操作；
**思路：**在*Operation*中添加一个校验的抽象方法：
  ```java
  /**
    * 校验数据
    * @param ec
    * @return 返回true：执行下一操作
    */
  protected abstract boolean validate(ElectronicComponent ec);
  ```
  在*doOperation*方法获取到数据后调用，修改后方法如下：
  ```java
  public void doOperation(Operation oper) {
    Thread thread = new Thread(new Runnable() {
      @Override
      public void run() {
        ElectronicComponent ec = null;
        while (true) {
          if ((ec = queue.poll()) != null && validate(ec)) {
            //do sth.....
          }
        }
      }
    });
    thread.setDaemon(false);
    thread.start();
  }
```
  在继承*Operation*时多实现一个*validate*方法即可(以DaMO类为例，这里为演示，只是判断是否为空)：
```java
package com.hulala.jmt.pc1.operation;
import com.hulala.jmt.pc1.pojo.ElectronicComponent;
public class DaMo extends Operation {
	@Override
	protected void operation(ElectronicComponent ec) {
		ec.status("打磨");
	}
	@Override
	protected boolean validate(ElectronicComponent ec) {
		return ec != null;
	}
}
```

