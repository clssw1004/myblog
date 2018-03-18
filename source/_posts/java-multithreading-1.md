---
title: JAVA多线程编程——生产者消费者模式(1)
tags:
  - Java
  - 多线程编程
  - 生产者消费者问题
comments: true
categories: Java
date: 2016-07-17 13:52:07
---

## 生产者消费者问题简述
{% blockquote @Wiki https://zh.wikipedia.org/wiki/%E7%94%9F%E4%BA%A7%E8%80%85%E6%B6%88%E8%B4%B9%E8%80%85%E9%97%AE%E9%A2%98 %}
**生产者消费者问题**（英语：Producer-consumer problem），也称有限缓冲问题（英语：Bounded-buffer problem），是一个多线程同步问题的经典案例。该问题描述了两个共享固定大小缓冲区的线程——即所谓的“生产者”和“消费者”——在实际运行时会发生的问题。生产者的主要作用是生成一定量的数据放到缓冲区中，然后重复此过程。与此同时，消费者也在缓冲区消耗这些数据。该问题的关键就是要保证生产者不会在缓冲区满时加入数据，消费者也不会在缓冲区中空时消耗数据;
{% endblockquote %}
<!-- more -->
&ensp;
如下图所示：
&ensp;
{% asset_img  1-1.PNG 典型的生产者消费者模型 %}
## 阻塞队列
由以上模型可知缓冲区是建立起两个线程之间数据交互的桥梁；通常我们使用队列充当缓冲区，这里便引入*阻塞队列*的概念：
{% blockquote @引用来源 http://www.infoq.com/cn/articles/java-blocking-queue/ %}
- *阻塞队列*（BlockingQueue） 是一个支持两个附加操作的队列。这两个附加的操作是：在队列为空时，获取元素的线程会等待队列变为非空。当队列满时，存储元素的线程会等待队列可用。阻塞 队列常用于生产者和消费者的场景，生产者是往队列里添加元素的线程，消费者是从队列里拿元素的线程。阻塞队列就是生产者存放元素的容器，而消费者也只从容 器里拿元素。
{% endblockquote %}
<!-- more -->
## 简单的泛型阻塞队列的实现
```java
package com.hulala.jmt.pc1.pojo;

import java.util.LinkedList;
import java.util.Queue;

/**
 * 队列的实现，支持线程间安全的offer poll操作
 */
public class BlockingQueue<T> {
    private Queue<T> queue;

    public BlockingQueue() {
        queue = new LinkedList<T>();
    }

    /**
     * 向队列尾部添加一个对象
     * 
     * @param t
     * @return
     */
    public synchronized boolean offer(T t) {
        boolean res = queue.offer(t);
        this.notify();
        return res;
    }

    /**
     * 从队列头部取出一个对象
     * 
     * @return
     * @throws InterruptedException
     */
    public synchronized T poll() {
        if (queue.size() == 0) {
            try {
                this.wait();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        return this.queue.poll();
    }

    /**
     * 返回队列长度
     * 
     * @return
     */
    public int size() {
        return this.queue.size();
    }
}
```
  代码非常简单，就是通过对Queue对象进行二次封装，这里使用到了synchronized关键字：
{% blockquote @引用来源 http://blog.csdn.net/luoweifu/article/details/46613015 %}
*synchronized*是Java中的关键字，是一种同步锁。它修饰的对象有以下几种
  1. 修饰一个代码块，被修饰的代码块称为同步语句块，其作用的范围是大括号{}括起来的代码，作用的对象是调用这个代码块的对象； 
  2. 修饰一个方法，被修饰的方法称为同步方法，其作用的范围是整个方法，作用的对象是调用这个方法的对象；
  3. 修改一个静态的方法，其作用的范围是整个静态方法，作用的对象是这个类的所有对象； 
  4. 修改一个类，其作用的范围是synchronized后面括号括起来的部分，作用主的对象是这个类的所有对象。
{% endblockquote %}
## 生产者消费者模式初步实现
下面来看看利用以上阻塞队列的*生产者消费者模型*的初步实现：
```java
package com.hulala.jmt.pc1;

import com.hulala.jmt.pc1.pojo.BlockingQueue;

public class App {
	public static void main(String[] args) {
		final BlockingQueue<String> queue = new BlockingQueue<String>();
		//生产者线程
		Thread cosumer = new Thread(new Runnable() {
			@Override
			public void run() {
				System.out.println(queue.poll());
			}
		});
		cosumer.start();
		try {
			Thread.sleep(3000);
		} catch (InterruptedException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
		//消费者线程
		Thread producer = new Thread(new Runnable() {
			@Override
			public void run() {
				queue.offer("I am here");
			}
		});
		producer.start();
	}
}
```
  执行以上代码，会看到控制台输出：
```
wait data
I am here
```
  **代码执行流程如下：**
  - 在调用**poll**方法时,在代码块synchronized中会获得当前BlockingQueue实例的对象锁，若队列中无数据，则wait方法会使当前线程进入休眠等待状态，并释放对象锁；
  - 在调用**offer**方法往队尾部添加数据之后，使用notify会时poll方法所在线程重新获取到当前BlockingQueue实例的对象锁，并继续执行wait方法后的代码；
  - 以上测试代码即是一个简单的生产者消费者模型，阻塞队列常用场景就是在生产者消费者问题中。

## 进阶
显而易见的是，实际情况不会像以上例子那么简单，生产者消费者问题，是一个生产者持续产生数据到缓冲区、而消费者持续消耗缓冲区数据的过程；下面我们通过一个例子来说明；
现以假设某个电子元件的生产流水线为例：
&ensp;
{% asset_img  1-2.PNG 流水线 %}

通过上图可以很简单的看出这条流水线由4个流程组成，下面以面向对象的思想分析以上流程：
1. 4个过程(打磨、抛光、焊接、质检)可抽象为4个操作
2. 每个操作本身都是相对于前一操作来说是消费者，相对于后一操作来说是生产者；
3. 每个操作实现不同（打磨、抛光、焊接、质检）

**面向对象编程的思想**要求我们找出对象的共同点在对其封装，对其不同点进行抽象。从以上分析我们已找到异同点，这便足以让我们开始写代码了；

首先 把电子元件抽象成一个Pojo:
```java
package com.hulala.jmt.pc1.pojo;

public class ElectronicComponent {
	public int code;

	public ElectronicComponent(int code) {
		this.code = code;
	}
	public void status(String opertion)
	{
		System.out.println("编号：" + code  + "正在执行\"" + opertion +"\"操作");
	}
}
```
然后是抽象出每个操作：
```java
package com.hulala.jmt.pc1;

public abstract class Operation {
	protected abstract void operation(ElectronicComponent ec);
}
```
因为这边的操作是个抽象概念，并不涉及某个实际内容，所以只需声明函数签名，无需实现函数体，故使用抽象方法（在C++中为虚函数）。

现在已经抽象出不同点了，下面就要继续实现各个模块共同点，共同点由三个：入口、出口、传送带，抽象出来就是数据接收、数据产生、缓冲区(*BlockingQueue*)，可见这就是一个典型的生产者消费者模型：
```java
package com.hulala.jmt.pc1;

public abstract class Operation {
	// 缓冲区
	private BlockingQueue<ElectronicComponent> queue;

	public Operation() {
		queue = new BlockingQueue<ElectronicComponent>();
	}

	/**
	 * 数据接收
	 * 
	 * @param ec
	 */
	public void receive(ElectronicComponent ec) {
		queue.offer(ec);
	}

	/**
	 * 数据传送
	 * 
	 * @param oper
	 * @param ec
	 */
	public void send(Operation oper, ElectronicComponent ec) {
		oper.receive(ec);
	}
}
```
现在一个模块的骨架基本完成了，但是光这样还不够，上面说了：生产者消费者模式运行起来是个是个持续过程，所以我们要有一个线程，确保模块接收数据同时可以执行操作：
```java
public void doOperation(Operation oper) {
  Thread thread = new Thread(new Runnable() {
    @Override
    public void run() {
      ElectronicComponent ec = null;
      if ((ec = queue.poll()) != null) {
        operation(ec);
        if (null != oper)
          send(oper, ec);
      }

    }
  });
  thread.setDaemon(false);
  thread.start();
}
```
**doOperation**目的很明确，启动一个线程，循环从队列中取出数据，执行该模块的operation操作，执行完后传送给下一个模块；
现在一个抽象的操作模块已经基本完成，可以具体实现流水线的4个操作了：
```java
DaMo.java
public class DaMo extends Operation {
	@Override
	protected void operation(ElectronicComponent ec) {
		ec.status("打磨");
	}
}

PaoGuang.java
public class PaoGuang extends Operation {
	@Override
	protected void operation(ElectronicComponent ec) {
		ec.status("抛光");
	}
}

HanJie.java
public class HanJie extends Operation {
	@Override
	protected void operation(ElectronicComponent ec) {
		ec.status("焊接");
	}
}

ZhiJian.java
public class ZhiJian extends Operation {
	@Override
	protected void operation(ElectronicComponent ec) {
		ec.status("质检");
	}
}
```
写了那么多，终于可以测试一下看看了:
```java
//App2.java
package com.hulala.jmt.pc1;

import com.hulala.jmt.pc1.operation.DaMo;
import com.hulala.jmt.pc1.operation.HanJie;
import com.hulala.jmt.pc1.operation.Operation;
import com.hulala.jmt.pc1.operation.PaoGuang;
import com.hulala.jmt.pc1.operation.ZhiJian;
import com.hulala.jmt.pc1.pojo.ElectronicComponent;

public class App2 {
	public static void main(String[] args) {
		Operation damo = new DaMo();
		Operation paoguang = new PaoGuang();
		Operation hanjie = new HanJie();
		Operation zhijian = new ZhiJian();
		zhijian.doOperation(null);
		hanjie.doOperation(zhijian);
		paoguang.doOperation(hanjie);
		damo.doOperation(paoguang);
		for (int i = 0; i != 5; ++i) {
			damo.receive(new ElectronicComponent(i));
		}
	}
}
```
运行以上main方法，看控制台输出：
```
编号：1正在执行"打磨"操作
编号：2正在执行"打磨"操作
编号：0正在执行"抛光"操作
编号：3正在执行"打磨"操作
编号：4正在执行"打磨"操作
编号：0正在执行"焊接"操作
编号：1正在执行"抛光"操作
编号：0正在执行"质检"操作
编号：2正在执行"抛光"操作
编号：1正在执行"焊接"操作
编号：3正在执行"抛光"操作
编号：1正在执行"质检"操作
编号：2正在执行"焊接"操作
编号：4正在执行"抛光"操作
编号：3正在执行"焊接"操作
编号：2正在执行"质检"操作
编号：4正在执行"焊接"操作
编号：3正在执行"质检"操作
编号：4正在执行"质检"操作
```
因为是多个线程异步执行，所以每次运行打出顺序可能会不一样。
## 总结
到此为止，一个完整的流水线算是初步完成了，在上面分析及设计过程中：
- 封装对象的共同点，抽象对象的不同点是程序设计实现一个非常好的思路；
- 我们都用到了面向对象的三个基本特征：继承、封装、多态，类图关系如下：
{% plantuml %}
package com.hulala.jmt.pc1.operation{
    abstract class Operation {
        -BlockingQueue<ElectronicComponent> queue
        +{abstract}operation(ElectronicComponent ec)
		+void receive(ElectronicComponent ec)
		+void doOperation(Operation oper)
		+void send(Operation oper, ElectronicComponent ec)

    }
    class DaMo extends Operation{
    }
	class PaoGuang extends Operation{
    }
	class HanJie extends Operation{
    }
	class ZhiJian extends Operation{
    }
}
{% endplantuml %}
- 生产者消费者模式是我们在多线程编程当中经常碰到的问题，掌握该模式足以让我们应付绝大多数多个线程之间数据交互的问题。

以上代码未考虑缓冲区溢出，省略了异常捕获，要真正用于生产环境这些都是要考略的。

## Github
完整代码的Github项目地址：*[producer-consumer](https://github.com/hulala1021/producer-consumer)*
&ensp;
{% githubCard hulala1021 producer-consumer %}
&ensp;