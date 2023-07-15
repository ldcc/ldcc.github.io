---
layout: post
title: 一个简单的线程同步读写文件操作
categories: 荤菜
tags: java thread I/O programming
date: 2016-09-21
---

最近在学 jawa，看完线程同步想写个关于 IO 的线程同步来试手，实践得真知。

### __同步代码块__

当多个线程同时处理共享资源时，就可能会引起线程安全问题，同步代码块能够有效解决线程的安全问题。

{% highlight java %}
static Object lock = ...;
public void run() {
	...
	synchronized(lock) {
		...
	}
}
{% endhighlight %}

lock 充当了一个锁的功能，同步代码块的代码在执行时会先检查自己有没有该对象的读写权限。

### __同步方法__

当一个方法的所有代码都在同步代码块的区域内时，可以用 `synchronized` 修饰该方法：

{% highlight java %}
public synchronized void foo() {
	...
}
{% endhighlight %}

同步代码块的锁可以是自定义的任意对象，同步方法也有锁，它的锁就是当前调用该方法的对象，即 `this`。

`void wait()` 可使当前线程放弃同步锁并进入等待。

`void notify()` 可唤醒此同步锁上等待的第一个调用wait()方法的线程。

## 代码

{% highlight java %}
public class IOStuff {

	byte [] b = new byte[1024];
	int temp = 0;
	
	BufferedInputStream bis;
	public synchronized void InputStuff(int n) throws IOException {
		File inURL = new File("from.txt");
		try {
			bis = new BufferedInputStream(new FileInputStream(inURL));
			while (temp != -1) {
				temp = bis.read(b);
				if (temp != -1) System.out.println("输入第" + ++n + "次");
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
				System.out.println("输出第" + ++n + "次");
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

	public void runIO() {
		new Thread(new MyInput(this)).start();
		new Thread(new MyOutput(this)).start();
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
