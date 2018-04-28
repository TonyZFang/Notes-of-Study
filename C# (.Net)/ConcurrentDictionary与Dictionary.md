ConcurrentDictionary与Dictionary

ASP.NET中ConcurrentDictionary是.Net4 增加的，相对于Dictionary的线程安全的集合， ConcurrentDictionary可实现一个线程安全的集合，可以由多个线程同时并发读写Key-value，与Dictionary的区别主要有以下几点

一、Dictionary<TKey, TValue>

1、泛型类提供了从一组键到一组值的映射。通过键来检索值的速度是非常快的，这是因为 Dictionary<TKey,TValue> 类是作为一个哈希表来实现的。检索速度取决于为 TKey 指定的类型的哈希算法的质量。

2、TValue可以是值类型，数组，类或其他。

3、一个类的实例里，有个属性是个字典，我们不加考虑的会用Dictionary，而当这个属性被提升为static静态的（类级别的）时候，我们就要考虑它的线程安全性了，因为它有可能被多个线程同时访问，当然，如果这个对象是只读的，也无所谓线程安全，但如果这个属性是可以被写的，那就需要把它加锁了。

4、若允许多个线程对集合执行读写操作，您必须实现自己的同步，读写并行的时候需要加锁，但是加上锁性能就会受影响。

二、ConcurrentDictionary<TKey, TValue>

1、表示可由多个线程同时访问的键/值对的线程安全集合。

2、ConcurrentDictionary是.net4.0推出的一套线程安全集合里的其中一个，和它一起被发行的还有

ConcurrentStack,ConcurrentQueue等类型，它们的单线程版本（线程不安全的,Queue,Stack,Dictionary）。

3、用法同Dictionary很多相同，但是多了一些方法。ConcurrentDictionary 属于System.Collections.Concurrent 命名空间

三、ConcurrentDictionary与Dictionary的区别实例

在多线程环境下，下面实例判断Key是否存在

public class Program
{
       static Dictionary<string, Logger> loggreDic;
       static object loggerDicLocker = new object();
       public static void Main()
       {
           loggreDic = new Dictionary<string, Logger>();
           for (int i = 0; i < 100; i++)
           {
               ThreadPool.QueueUserWorkItem(o =>
               {
                   try
                   {
                       var logger = GetLogger("AAA");
                   }
                   catch (Exception)
                   {
                       Console.WriteLine(string.Format("第{0}个线程出现问题", o));
                   }

               }, i);
           }
           Console.ReadKey();
       }

       static Logger GetLogger(string cmdId)
       {
           if (!loggreDic.ContainsKey(cmdId))
           {
               loggreDic.Add(cmdId, LogManager.GetLogger(string.Format("AAA.{0}", cmdId)));
           }
           return loggreDic[cmdId];
       }
   }

在GetLogger的地方做了判断的处理，但是在多线程的时候还是会报 已添加了具有相同键的项 的错误。原因是在Dictionary中加了重复的Key造成的。

解决方法

可以用ConcurrentDictionary 替代Dictionary
public interface IGetLogger
{
        Logger GetLogger(string cmdId);
}

public class ConcurrentDictionaryLogger : IGetLogger
    {
        ConcurrentDictionary<string, Logger> loggreDic = new ConcurrentDictionary<string, Logger>();
        public Logger GetLogger(string cmdId)
        {
            if (!loggreDic.ContainsKey(cmdId))
            {
                loggreDic.TryAdd(cmdId, LogManager.GetLogger(string.Format("AAA.{0}", cmdId)));
            }
            return loggreDic[cmdId];
        }
    }

    public static void Main()
    {
            IGetLogger conLogger = new ConcurrentDictionaryLogger();

            IGetLogger lockerLogger = new LockerDictionaryLogger();

            CodeTimer.Time("使用ConcurrentDictionary", 1000000, () =>
            {
                ThreadPool.QueueUserWorkItem(o =>
                {
                    try
                    {
                        var logger = conLogger.GetLogger("AAA");
                        if (logger == null)
                        {
                            Console.WriteLine(string.Format("第{0}个线程获取到的值是 NULL", o));
                        }
                    }
                    catch (Exception ex)
                    {
                        Console.WriteLine(string.Format("第{0}个线程出现问题, {1}", o, ex.Message));
                    }
                });
         });
   }

原文链接:https://www.cnblogs.com/blogs2014/p/7682245.html
