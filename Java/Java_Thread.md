# 多线程创建方法
```
1.继承Thread类
2.实现Runnable接口
3.实现Callable接口
4.线程池
```

# 继承Thread类
```
1.创建一个继承于Thread类的子类
class MyThread extends Thread{}

2.重写Thread类的run方法
@Override
public void run(){}

3.创建Thread类的子类的对象
MyThread myThread = new MyThread();

4.通过此对象调用start()
myThread.start(); // 不可以对同一个Thread子类对象使用两次start方法，会报IllegalThreadStateException

start方法的作用：1.启动当前线程  2.调用当前线程的run方法
获取当前线程：Thread.currentThread()
获取当前线程名称：Thread.currentThread().getName()
```

# Thread匿名子类
```
new Thread(){
    @Override
    public void run(){
        //代码
    }
}.start();
```

# Thread类中常用方法
```
start():启动当前线程，调用当前线程的run()
run():需要被重写，将创建的线程要执行的操作声明在此方法中
currentThread():静态方法，返回执行当前代码的线程
getName():获取当前线程的名字
setName():设置当前线程的名字
yield():释放当前cpu的执行权
join():在线程a中调用线程b的join()，线程a会进入阻塞状态，直到b完全执行完，线程a才结束阻塞状态
```

# 线程优先级
```
MAX_PRIORITY:10
MIN_PRIORITY:1
NORM_PRIORITY:5 //默认优先级

获取当前优先级：
getPriority()获取线程的优先级
setPriority(int p):设置线程优先级
从概率上讲，高优先级线程更有机会获取CPU执行权
```

# 实现Runnable接口
```
1.创建一个实现了Runnable接口的类
class MyThread implements Runnable{}

2.实现抽象方法run()
public void run(){}

3.创建实现类的对象
MyThread myThread = new MyThread();

4.将此对象作为参数传递到Thread类的构造器中，创建Thread对象
Thread thread = new Thread(myThread);

5.通过Thread对象调用start()
thread.start();
```

# Thread中定义的线程状态State
```
新建：对象被声明并创建
就绪：被start()，进入线程队列，具备运行条件但是没有分配到CPU资源
运行：获得了CPU资源，进入运行状态
阻塞：被人为挂起或执行输入输出操作，让CPU临时中断自己的执行，进入阻塞状态
死亡：线程完成全部工作或线程被提前强制性终止或出现异常
```

# 同步机制（解决线程安全问题）
```
方法1：同步代码块
    synchronized(同步监视器){}
说明：
    1.代码块里面放需要操作共享数据的代码
    2.共享数据：多个线程共同操作的变量
    3.同步监视器（锁）：任何一个对象，都可以充当锁（多个线程必须共用一把锁）
    4.在实现Runnable接口创建多线程的方式中，可以考虑用this充当同步监视器
    5.在Thread继承类中，应该用类名.class充当监视器

方法2：同步方法
    用synchronized修饰方法，例如：
    public synchronized void show(){}
说明：
    1.同步方法依然涉及到同步监视器，只是不需要显示说明
    2.非静态同步方法，同步监视器是this（Runnable好用）
      静态方法，同步监视器是当前类本身(类名.class)
    
方法3：Lock
    从JDK5.0开始Java提供了显示定义同步锁对象来实现同步，同步锁使用Lock对象充当
    java.util.concurrent.locks.Lock接口是控制多个线程对共享资源访问的工具
    ReentrantLock类实现了Lock，拥有与synchronized相同的并发性和内存语义，能够显示的加锁，释放锁
    用法：
    1.实例化ReentrantLock对象
    private ReentrantLock lock = new ReentrantLock();
    2.调用lock方法
    lock.lock();
    3.调用解锁方法
    lock.unlock();
```

# 线程通信
```
三个方法：
wait():一旦执行此方法，当前线程进入阻塞状态，并释放同步监视器
notify():唤醒被wait的一个线程，根据优先级唤醒
notifyAll()：唤醒所有被wait的线程
说明：
1.wait,notify,notifyAll三个方法必须使用在同步代码块或同步方法中
2.这三个方法的调用者必须是同步代码块或同步方法中的同步监视器，否则会出现IllegalMonitorStateException异常
3.三个方法都是定义在java.lang.Object中
```

# 实现Callable接口
```
1.创建一个实现Callable的实现类
class NumThread implements Callable{}

2.实现call方法，此线程需要执行的操作声明在call()中
@Override
public Object call() throws Exception{}

3.创建Callable接口实现类对象
NumThread numThread = new NumThread();

4.将此Callable接口的实现类的对象传递到FutureTask构造器中，创建FutureTask对象
FutureTask futureTask = new FutureTask(numThread);

5.将Future对象传递到Thread类构造器中，创建Thread对象，并调用start()
new Thread(futureTask).start();

6.获取Callable中的call方法返回值（可选）
Object sum = futureTask.get();

说明：Callable比Runnable强大的点：
1.call()可以有返回值
2.call()可以抛出异常
3.Callable支持泛型
```

# 线程池
```
1.提供指定线程数量的线程池
ExecutorService service = Executors.newFixedThreadPool(10);

2.执行指定的线程操作，需要提供实现Runnable接口或Callable接口实现类对象
service.execute(new NumberThread());
serivce.execute(new NumberThread1());

3.关闭线程池
service.shutdown();

线程池好处：
1.提高响应速度（减少创建新线程的时间）
2.降低资源消耗（重复利用线程池中的线程，不需要多次创建）
3.便于线程管理：
    corePoolSize：核心池大小
    maximumPoolSize：最大线程数
    keepAliveTime：线程没有任务时最多保持多长时间会终止
    ThreadPoolExecutor service1 = (ThreadPoolExecutor)service;
    service1.setCorePoolSize();
    service1.setKeepAliveTime();
```