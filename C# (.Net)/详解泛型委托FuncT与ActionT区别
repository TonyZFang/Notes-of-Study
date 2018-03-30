详解Func<T>与Action<T>区别

一、Func
Func是一个.Net内置的委托。
Func<Result>，Func<T1,Result>是一个.Net内置的泛型委托。

Func<TResult>
Func<T,TResult>
Func<T1,T2,TResult>
Func<T1,T2,T3,TResult>
Func<T1,T2,T3,T4,TResult>
它有5种形式，只是参数个数不同；第一个是无参数，但是有返回值；

下面是一个简单的普通委托来传方法的示例
private delegate string Say();
public static string SayHello()
{
  return "Hello";
}
static void Main(string[] args)
{
  Say say = SayHello;
  Console.WriteLine(say());
  Console.ReadKey();
}

所以，在有时候，我们不知道一个接口同时要做什么操作的时候，我可以给它留一个委托。

为了更方便，.Net直接默认有了委托。我们再来试试.Net默认带的委托。
public static string SayHello()
{
  return "Hello";
}
static void Main(string[] args)
{
  Func<string> say = SayHello;
  Console.WriteLine(say());
  Console.ReadKey();
}

如果需要参数的，还可以这样传一份。
public static string SayHello(string str)
{
  return str + str;
}
static void Main(string[] args)
{
  Func<string, string> say = SayHello;
  string str = say("abc");
  Console.WriteLine(str);   //输出abcabc
  Console.ReadKey();
}

二、Action
Action<T>的用法与Func几乎一样，调用方法也类似。
Action
Action<T>
Action<T1,T2>
Action<T1,T2,T3>
Action<T1,T2,T3,T4>

private delegate string Say();
public static void SayHello(string str)
{
  Console.WriteLine(str);
}
static void Main(string[] args)
{
  Action<string> say = SayHello;
  say("abc");
  Console.ReadKey();
}

三、Func与Action的区别
Func与Action作用几乎一样。只是
Func<Result>有返回类型；
Action<T>只有参数类型，不能传返回类型。所以Action<T>的委托函数都是没有返回值的。

四、Func与Action都支持Lambda的形式调用
还是以一个输入后，返回重复一次的值作为示例。

Func<string, string> say = m => m + m;
Console.WriteLine(say("abc"));　　　　//输出abcabc

五、最常见到Func的地方
通常我们最常见到Func是在方法的参数里如下面这样：
string XXX(Func<string, string>)

咱们来看看Linq里面的其中一个Sum：
public static int Sum<TSource>(this IEnumerable<TSource> source, Func<TSource, int> selector);
里面看到两点：
1、扩展方法，与这篇文章无关(扩展的是IEnumerable<TSource>，主要是为了能够实现IEnumerable<TSource>接口的集合.出函数)。
2、Func<TSource, int> selector这个参数。

尝试写一个Linq的First函数吧，命名为First2。Linq源代码里有很多异常情况处理，好多设计模式，可惜我不懂，只提取简单逻辑了。
namespace ConsoleApplication2
{
  static class Extend
  {
    public static TSource First2<TSource>(this IEnumerable<TSource> source, Func<TSource, bool> predicate)
    {
      //.Net本身的源代码好多异常情况处理，好多设计模式，我也不懂，只提取逻辑
      foreach (TSource item in source)
      {
        if (predicate(item))
        {
          return (item);
        }
      }
      throw new Exception("不存在满足条件的第一个元素！");
    }
  }
  class Program
  {
    static void Main(string[] args)
    {
      List<int> ListInt = new List<int>(){ 1, 2, 3, 4, 5 };
      int k = ListInt.First2(m => m > 4);   //输出5
      Console.WriteLine(k);

      Console.ReadKey();
    }
  }
}

原文链接:http://www.jb51.net/article/97821.htm
