---
layout: post
title: 线程同步读写文件
category: algorithm
date: 2016-09-21
---

看完线程同步想写个io流的线程同步来试手，于是。。

### 同步代码块

当多个线程同时处理共享资源时，就会引起线程安全问题， 同步代码块能够有效解决线程的安全问题。

{% highlight java %}

static Object lock = ...;
public void run() {
	...
	synchronized(lock) {
		...
	}
}

{% endhighlight %}

lock是一个锁对象，锁对象的创建代码不能放到run()方法中。否则每个线程运行时都会创建一个新对象，线程之间便不能产生同步的效果。

### 同步方法

当一个方法的所有代码都在同步代码块的区域内时，可以用 `synchronized` 修饰该方法。同步方法和同步代码块的功能基本一致，具体语法如下：

{% highlight java %}

public synchronized void doSomething() {
	...
}

{% endhighlight %}

同步代码块的锁是自己定义的任意类型的对象，同步方法也有锁，它的锁就是当前调用该方法的对象，也就是 `this`指向的对象。

`void wait()`

使当前线程放弃同步锁并进入等待。

`void notify()`

唤醒此同步锁上等待的第一个调用wait()方法的线程。

## 代码

{% highlight java %}

public class IOStuff {

	static byte [] b = new byte[1024];
	static int temp;
	
	BufferedInputStream bis;
	public synchronized void InputStuff(int n) throws IOException {
		File inURL = new File("from.txt");
		try {
			bis = new BufferedInputStream(new FileInputStream(inURL));
			while (temp != -1) {
				temp = bis.read(b);
				if (temp != -1) System.out.println("读取第" + ++n + "次");
				this.notify();
				this.wait();
			}
		} catch (IOException e) {
			e.printStackTrace();
		} finally {
			bis.close();
		}
	}
	
	BufferedOutputStream bos;
	public synchronized void OutputStuff(int n) throws IOException {
		File outURL = new File("to.txt");
		try {
			bos = new BufferedOutputStream(new FileOutputStream(outURL));
			while (temp != -1) {
				bos.write(b, 0, temp);
				System.out.println("写入第" + ++n + "次");
				this.notify();
				this.wait();
			}
		} catch (FileNotFoundException e) {
			e.printStackTrace();
		} catch (IOException e) {
			e.printStackTrace();
		} finally {
			bos.close();
		}
	}

	public void runIO(IOStuff ios) {
		new Thread(new MyInput(ios)).start();
		new Thread(new MyOutput(ios)).start();
	}
}

public class MyInput implements Runnable {

	private IOStuff ios;
	public MyInput(IOStuff ios) {
		this.ios = ios;
	}
	
	@Override
	public void run() {
		try {
			ios.InputStuff(0);
		} catch (Exception e) {
			e.printStackTrace();
		}
	}
}

public class MyOutput implements Runnable {

	private IOStuff ios;
	public MyOutput(IOStuff ios) {
		this.ios = ios;
	}

	@Override
	public void run() {
		try {
			ios.OutputStuff(0);
		} catch (Exception e) {
			e.printStackTrace();
		}
	}
}

{% endhighlight %}

## 测试

{% highlight java %}

public class Test {

	public static void main(String[] args) {
		IOStuff ios = new IOStuff();
		ios.runIO();
	}
}

{% endhighlight %}

我的结果是：

![result](/images/thsync-java/result.png)
