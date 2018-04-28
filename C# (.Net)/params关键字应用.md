c#中params关键字应用

params 是C#开发语言中关键字， params主要的用处是在给函数传参数的时候用，就是当函数的参数不固定的时候。 在方法声明中的 params 关键字之后不允许任何其他参数，并且在方法声明中只允许一个 params 关键字。 关于参数数组，需掌握以下几点。 　　

（1）若形参表中含一个参数数组，则该参数数组必须位于形参列表的最后； 　　

（2）参数数组必须是一维数组； 　　

（3）不允许将params修饰符与ref和out修饰符组合起来使用； 　　

（4）与参数数组对应的实参可以是同一类型的数组名，也可以是任意多个与该数组的元素属于同一类型的变量； 　　

（5）若实参是数组则按引用传递，若实参是变量或表达式则按值传递。 　　

（6）用法：可变的方法参数，也称数组型参数，适合于方法的参数个数不知的情况，用于传递大量的数组集合参数；当使用数组参数时，可通过使用params关键字在形参表中指定多种方法参数，并在方法的参数表中指定一个数组，

形式为：方法修饰符　返回类型　方法名（params　类型［］　变量名）
如带有参数的SQL 语句，不同的表的字段数量也不同， 当你更新修改的时候就可以用。例如：

params using System;

public class MyClass

{

    public static void UseParams(params int[] list)

   {

       for (int i = 0 ; i < list.Length; i++)

      { Console.WriteLine(list[i]); }

      Console.WriteLine();

   }

   public static void UseParams2(params object[] list)

   {

       for (int i = 0 ; i < list.Length; i++)

      { Console.WriteLine(list[i]); }

      Console.WriteLine();

   }

   static void Main()

   {

　　　　UseParams(1, 2, 3);

　　　　UseParams2(1, 'a', "test");

　　　　 // An array of objects can also be passed, as long as

　　　　// the array type matches the method being called.

　　　　int[] myarray = new int[3] {10,11,12};

　　　　UseParams(myarray);

　　}

}

　　输出：1231atest101112

原文链接:https://www.cnblogs.com/yiyi20120822/archive/2012/11/29/2794009.html
