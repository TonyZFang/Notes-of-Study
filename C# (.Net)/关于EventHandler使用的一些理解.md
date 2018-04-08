关于EventHandler使用的一些理解

阅读《C# 高级编程》，其中关于EventHandler的使用颇让人费解。

因为已经习惯了public event delegateName eventName;这种定义事件的方式，因此下面结合这种方式来解释一下书中示例的代码。

先来看看CarDealer类的定义：
<span id="transmark" style="display: none; width: 0px; height: 0px;"></span>public class CarInfoEventArgs : EventArgs
{
  public CarInfoEventArgs(string car)
  {
    this.Car = car;
  }

  public string Car { get; private set; }
}

public class CarDealer
{
  public event EventHandler<CarInfoEventArgs> NewCarInfo;

  public void NewCar(string car)
  {
    Console.WriteLine("CarDealer, new car {0}", car);

    RaiseNewCarInfo(car);
  }

  protected virtual void RaiseNewCarInfo(string car)
  {
    EventHandler<CarInfoEventArgs> newCarInfo = NewCarInfo;
    if (newCarInfo != null)
    {
      newCarInfo(this, new CarInfoEventArgs(car));
    }
  }
}

这里实际上先定义了一个继承自EventArgs的类，这个类的作用是用来储存将用来传递给事件的参数，在本例中，就是string成员Car，表示新车的名字。
然后在CarDealer类中，定义了事件NewCarInfo，类型是EventHandler<CarInfoEventArgs>，这是EventHanlder的泛型。EventHandler<T>表示一个接受两个参数(object sender, TEventArgs e)，返回类型为void的方法。其中，TEventArgs必须为派生自EventArgs类的类型。后一个参数就是储存事件所需要参数用的。

然后是两个方法，其中RaiseNewCarInfo方法就是执行事件的方法。该方法由NewCar方法调用，接受string参数。而NewCar方法开放给汽车销售商，每到一辆新车，就执行一次这个方法，执行需要把新车的名字传递给方法。

然后我们来看客户Consumer类：
public class Consumer

   private string name;

   public Consumer(string name)
   {
       this.name = name;
   }

   public void NewCarIsHere(object sender, CarInfoEventArgs e)
   {
       Console.WriteLine("{0}: car {1} is new", name, e.Car);
   }

   客户类定义了字段name用来储存客户名，这个name会在NewCarIsHere方法中使用到。重点就是这个NewCarIsHere方法，观察可知，它的签名符合EventHandler<CarInfoEventArgs>类型：返回类型为void，接受一个object参数和一个CarInfoEventArgs参数。这就是Event事件可以接受的方法。
因此在Main类中执行的方法就很简单了：
var dealer = new CarDealer();

var michael = new Consumer("Michael");
dealer.NewCarInfo += michael.NewCarIsHere;

dealer.NewCar("Ferrari");

var nick = new Consumer("Sebastian");
dealer.NewCarInfo += nick.NewCarIsHere;

dealer.NewCar("Mercedes");

dealer.NewCarInfo -= michael.NewCarIsHere;

dealer.NewCar("Red Bull Racing");

将不同Consumer实例中的NewCarIsHere方法加入NewCarInfo事件，于是在每次销售商执行NewCar方法时，两个客户都会得到通知。

其实，以上可以写成：
public class NewCarDealer
{
    public delegate void NewCarDelegate(string name);
    public event NewCarDelegate NewCarEvent;

    public void NewCar(string name)
    {
        Console.WriteLine("CarDealer, new car {0}", name);
        if (NewCarEvent!=null)
        {
            NewCarEvent(name);
        }
    }
}

public class NewConsumer
{
    private string name;

    public NewConsumer(string name)
    {
        this.name = name;
    }

    public void NewCarIsHere(string carName)
    {
        Console.WriteLine("{0}: car {1} is new", name, carName);
    }
}

然后在Main方法中执行以下代码：

var dealer = new NewCarDealer();

var michael = new NewConsumer("Michael");
dealer.NewCarEvent += michael.NewCarIsHere;

dealer.NewCar("Ferrari");

var nick = new NewConsumer("Sebastian");
dealer.NewCarEvent += nick.NewCarIsHere;

dealer.NewCar("Mercedes");

dealer.NewCarEvent -= michael.NewCarIsHere;

dealer.NewCar("Red Bull Racing");

可以得到一样的效果。

原文链接:https://blog.csdn.net/cyh1992899/article/details/52806690
