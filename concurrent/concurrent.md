## java内存模型

![内存图](https://github.com/yexiaofei123/doc/raw/master/img/1.png "内存图")
- voaltile 与 synchronized

```text
  voaltile关键字，使一个变量在多个线程中可见
  A、B线程都用到一个变量，java默认是A线程中保留一份copy，如果B线程修改了这个变量，则A线程未必知道
  使用voaltile关键字，会让所有线程都会读到变量得修改值
  举例：当线程t1开始运行得时候，会把count值从内存中读到t1线程得工作区，在运行过程中直接使用这个copy，
  不会每次去读取堆内存，这样当主线程修改count值之后，t1线程感知不到，所以不会停止运行
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
  
```