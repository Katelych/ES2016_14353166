# lab4 死锁

## 死锁停在第几次的截图：

  ![死锁](https://github.com/Katelych/ES2016_14353166/blob/master/assignment/lab4Deadlock/Deadlock.png?raw=true)

  可见，在第505次陷入死锁

## 产生死锁的4个必要条件
（1） 互斥条件：一个资源每次只能被一个进程使用。
（2） 请求与保持条件：一个进程因请求资源而阻塞时，对已获得的资源保持不放。
（3） 不剥夺条件:进程已获得的资源，在末使用完之前，不能强行剥夺。
（4） 循环等待条件:若干进程之间形成一种头尾相接的循环等待资源关系。
这四个条件是死锁的必要条件，只要系统发生死锁，这些条件必然成立，而只要上述条件之
一不满足，就不会发生死锁。

## 对上述程序产生死锁的解释

  ```
  class A{
	synchronized void methodA(B b){
		b.last();
	}

	synchronized void last(){
		System.out.println("Inside A.last()");
	}
}

  class B{
	synchronized void methodB(A a){
		a.last();
	}

	synchronized void last(){
		System.out.println("Inside B.last()");
	}
}


   class Deadlock implements Runnable{
	A a=new A();
	B b=new B();

	Deadlock(){
		Thread t = new Thread(this);
		int count = 20000;

		t.start();
		while(count-->0);
		a.methodA(b);
	}

	public void run(){
		b.methodB(a);
	}

	public static void main(String args[]){
		new Deadlock();
	}
}

   ```

  从以上代码可看出，这里涉及到两个线程，一个主线程main,一个子线程t,首先主线程运行，创建子线程t,执行a.methodA(b)语句,调用A类的methodA函数，并且正准备调用但还没调用B类的last()函数的时候时间片用完了，主线程被迫退出CPU，轮到子线程占用CPU执行b.methodB(a)函数，执行B类的methodB()函数，时间片又用完了，回到主线程main,想要调用b.last()却发现B类的被声明为synchronized的methodB()函数正在被调用着，还没结束，这时候同样声明为synchronized的b.last()就执行不了，干等着，等时间片用完切换到另一个线程：子线程t,此时要调用a.last()，同样的，A类的被声明为synchronized的methodA()函数正在被主线程调用着，声明为synchronized的a.last()就执行不了，也在干等着，等时间片用完切换到主线程，但也还是执行不了函数b.last()，这样就产生了死锁。