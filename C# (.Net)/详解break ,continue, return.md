详解C#break ,continue, return

C#编程语法中break ,continue, return这三个常用的关键字的学习对于我们编程开发是十分有用的，那么本文就向你介绍break ,continue, return具体的语法规范。

C#编程语法中我们会碰到break ,continue, return这三个常用的关键字，那么关于这三个关键字的使用具体的操作是什么呢？我们在使用这三关键字的时候需要注意和需要理解的规则是什么呢？让我们开始介绍吧：

一、C#编程语法之break语句：
break语句会使运行的程序立刻退出包含在最内层的循环或者退出一个switch语句。由于它是用来退出循环或者switch语句，所以只有当它出现在这些语句时，这种形式的break语句才是合法的。
如果一个循环的终止条件非常复杂，那么使用break语句来实现某些条件比用一个循环表达式来表达所有的条件容易得多。
for(var i=1;i<=10;i++)
{ if(i==6) break;
  document.write(i);
}
//输出结果：12345

二、C#编程语法之continue语句：
continue语句和break语句相似。所不同的是，它不是退出一个循环，而是开始循环的一次新迭代。
continue语句只能用在while语句、do/while语句、for语句、或者for/in语句的循环体内，在其它地方使用都会引起错误！
for(var i=1;i<=10;i++)
{ if(i==6) continue;
  document.write(i);
}
//输出结果：1234578910

三、C#编程语法之return语句：
return语句就是用于指定函数返回的值。return语句只能出现在函数体内，出现在代码中的其他任何地方都会造成语法错误！
当执行return语句时，即使函数主体中还有其他语句，函数执行也会停止！

原文地址:https://www.cnblogs.com/lgx5/p/6017920.html
