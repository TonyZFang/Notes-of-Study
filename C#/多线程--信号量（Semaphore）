多线程--信号量（Semaphore）

百度百科：Semaphore,是负责协调各个线程, 以保证它们能够正确、合理的使用公共资源。也是操作系统中用于控制进程同步互斥的量。
Semaphore常用的方法有两个 WaitOne()和 Release()，Release()的作用是退出信号量并返回前一个计数，而 WaitOne()则是阻止当前线程，直到当前线程的WaitHandle 收到信号。这里我举一个例子让大家更容易理解：当我们这样实例化Semaphore时候

Semaphore sema = new Semaphore(x,y)

有一队人排队上洗手间，人就相当于线程，x为还剩余的位置数量，y为总的位置数量。

WaitOne()方法就相当于人在等待洗手间位置的行为，而Release()方法就相当于一个人从洗手间出来的行为，这里再假设x和y都为5，说明开始的时候洗手间有5个空位置，且总共只有5个位置，当一队超过5个人的队伍要上洗手间的就排队，首先WaitOne()方法等待，发现有空位就依次进去，每进去一个空位减一，直到进去5之后个没有空位，这时候后面的人就一直等待，直到进去的人从洗手间出来Release()方法，空位加一，在等待WaitOne()方法的人发现有空位又进去一个空位减一……如此循环往复。

请看下面例子：

using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading;
using System.Threading.Tasks;

namespace TestSemaphore
{
    class Program
    {
        static Semaphore sema = new Semaphore(5, 5);
        const int cycleNum = 9;
        static void Main(string[] args)
        {
            for (int i = 0; i < cycleNum; i++)
            {
                Thread td = new Thread(new ParameterizedThreadStart(testFun));
                td.Name = string.Format("编号{0}", i.ToString());
                td.Start(td.Name);
            }
            Console.ReadKey();
        }
        public static void testFun(object obj)
        {
            sema.WaitOne();
            Console.WriteLine(obj.ToString() + "进洗手间：" + DateTime.Now.ToString());
            Thread.Sleep(2000);
            Console.WriteLine(obj.ToString() + "出洗手间：" + DateTime.Now.ToString());
            sema.Release();
        }
    }
}

运行结果如下：

编号5进洗手间：2018/3/29 11:45:44
编号0进洗手间：2018/3/29 11:45:44
编号1进洗手间：2018/3/29 11:45:44
编号3进洗手间：2018/3/29 11:45:44
编号2进洗手间：2018/3/29 11:45:44
编号5出洗手间：2018/3/29 11:45:46
编号4进洗手间：2018/3/29 11:45:46
编号2出洗手间：2018/3/29 11:45:46
编号6进洗手间：2018/3/29 11:45:46
编号1出洗手间：2018/3/29 11:45:46
编号7进洗手间：2018/3/29 11:45:46
编号3出洗手间：2018/3/29 11:45:46
编号0出洗手间：2018/3/29 11:45:46
编号8进洗手间：2018/3/29 11:45:46
编号4出洗手间：2018/3/29 11:45:48
编号6出洗手间：2018/3/29 11:45:48
编号7出洗手间：2018/3/29 11:45:48
编号8出洗手间：2018/3/29 11:45:48

这里我要说明一点，信号量控制的只是线程同步的量，而不管顺序，这个例子来说线程控制的就是线程同步量为5，也就是同时并发的线程数量为5个，至于是哪个先哪个后不是由这里的信号量决定的。

当然这个例子中因没有做什么复杂的操作，一般情况进入线程的时间和每个线程要的时间不会有太大差别，所以执行的顺序还是很规律的（为了说明这个问题我也是运行了多次才让结果稍有不同，这里编号2抢在了编号1前面就是这个道理），如果线程中有很复杂的操作每个线程在运行中所用的时间有比较大的差别，或者循环开始有复杂操作那么很可能就不是编号0先进入洗手间了，且不一定是先进入的就会先出来。

接下来再简单介绍一下 Semaphore的WaitOne()和Release()的重载方法

public int Release(int releaseCount);
releaseCount 指的是释放的信号量数量，如果没有参数默认为1，Release() 就相当于 Release(1)

这里要说明一点，当 Release() 或者 Release(int releaseCount) 执行时导致信号量计数大于最大数量时会抛出SemaphoreFullException异常。下面这种情况就会异常：

Semaphore sem = new Semaphore(4,5);
sem.Release(2);//这里是释放2个信号量加上之前的4个,超出5个了


public virtual bool WaitOne(TimeSpan timeout);
public virtual bool WaitOne(int millisecondsTimeout);
第一个重载参数timeout：指定时间间隔，若在这段时间内没有接收到信号则跳过等待继续执行

第二个重载参数millisecondsTimeout：指定时间间隔整数毫秒，若在这段时间内没有接收到信号则跳过等待继续执行

WaitOne()还有两个重载方法不是很常用这里就不介绍了。上面的重载方法这里也不再进了案例说明了，有兴趣的朋友可以自己尝试一下。

原文链接：https://www.cnblogs.com/yifengjianbai/p/5468449.html
