## java内存模型

![内存图](https://github.com/yexiaofei123/doc/raw/master/img/memory.png "内存图")
```text
 ①每个线程都有一个自己的本地内存空间--线程栈空间???线程执行时，先把变量从主内存读取到线程自己的本地内存空间，然后再对该变量进行操作
 
 ②对该变量操作完后，在某个时间再把变量刷新回主内存
```
- voaltile 与 synchronized

```text
  voaltile关键字，使一个变量在多个线程中可见
  A、B线程都用到一个变量，java默认是A线程中保留一份copy，如果B线程修改了这个变量，则A线程未必知道
  使用voaltile关键字，会让所有线程都会读到变量得修改值
  举例：当线程t1开始运行得时候，会把count值从内存中读到t1线程得工作区，在运行过程中直接使用这个copy，
  不会每次去读取堆内存，这样当主线程修改count值之后，t1线程感知不到，所以不会停止运行
  但是volatile并不能保证多个线程共同修改count变量所带来得不一致得问题，volatile并不能代替synchonized
```

- 可以阅读这篇文章进行更深入的理解
   http://www.cnblogs.com/nexiyi/p/java_memory_model_and_thread.html

```java
  public class T {
      /*volatile*/ boolean running = true; //对比一下有无volatile的情况下，整个程序运行结果的区别
  
      void m() {
          System.out.println("m start");
          while (running) {
  
  			/*try {
  				TimeUnit.MILLISECONDS.sleep(10);
  			} catch (InterruptedException e) {
  				e.printStackTrace();
  			}*/
          }
          System.out.println("m end!");
      }
  
      public static void main(String[] args) {
          T t = new T();
  
          new Thread(t::m, "t1").start();
  
          try {
              TimeUnit.SECONDS.sleep(1);
          } catch (InterruptedException e) {
              e.printStackTrace();
          }
  
          t.running = false;
      }
  }   
```
- synchronized

```text
  ①volatile轻量级，只能修饰变量。synchronized重量级，还可修饰方法
  
  ②volatile只能保证数据的可见性，不能用来同步，因为多个线程并发访问volatile修饰的变量不会阻塞。
  
  synchronized不仅保证可见性，而且还保证原子性，因为，只有获得了锁的线程才能进入临界区，从而保证临界区中的所有语句都全部执行。多个线程争抢synchronized锁对象时，会出现阻塞。
```

```java

public class T {
	/*volatile*/ int count = 0;

	synchronized void m() { 
		for (int i = 0; i < 10000; i++)
			count++;
	}

	public static void main(String[] args) {
		T t = new T();

		List<Thread> threads = new ArrayList<Thread>();

		for (int i = 0; i < 10; i++) {
			threads.add(new Thread(t::m, "thread-" + i));
		}

		threads.forEach((o) -> o.start());

		threads.forEach((o) -> {
			try {
				o.join();
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
		});

		System.out.println(t.count);

	}
}
```
- reentrantlock 可重入锁

```text
重入锁 必须手动释放锁 设置公平锁
```
- 队列
```text
ConcurrentLinkedQueue  非阻塞高并发队列 
LinkedBlockingQueue 无界队列 是链表实现得阻塞队列，在链表一头加入元素，如果队列满，就会阻塞，另一头取出元素，如果队列空，就会等待
ArrayBlockingQueue  有界队列 比如设置10个队列 当put进去10个 在put时就会阻塞
TransferQueue      直接交给消费者消费
SynchronusQueue    容量为0  生产了要马上消费
DelayQueue执行定时任务  在这个任务中的元素默认多长时间被消费者消费
```

- 线程
```text
http://www.cnblogs.com/zhujiabin/p/5404771.html
newCachedThreadPool 缓存的线程池 当来了2个任务就启动2个线程  当第三个任务来了时候前面线程执行完成后就不在启动第三个线程来执行。当前面的没
                     有执行完成就启动第三个线程执行
newSingleThreadExecutor 一个线程池
newFixedThreadPool 创建一个定长线程池，可控制线程最大并发数，超出的线程会在队列中等待。
newScheduledThreadPool 创建一个定长线程池，支持定时及周期性任务执行。
ThreadPoolExecutor( int maximumPoolSize, long keepAliveTime, TimeUnit unit, BlockingQueue<Runnable> workQueue,RejectedExecutionHandler handler)                                                                                                                                                                                                                       
    corePoolSize 核心线程池大小
    maximumPoolSize 线程池最大容量大小
    keepAliveTime 线程池空闲时，线程存活的时间
    TimeUnit 时间单位
    ThreadFactory 线程工厂
    BlockingQueue任务队列
    RejectedExecutionHandler 线程拒绝策略    
```