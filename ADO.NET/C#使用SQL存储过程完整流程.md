C#使用SQL存储过程完整流程

存储过程就是固化在SQL数据库系统内部的SQL语句，这样做的好处是可以提高执行效率、提高数据库的安全性、减少网络流量。接下来就讲解如何在数据库中建立一个存储过程。

打开SQL数据库，展开“数据库”节点，找到你使用的数据库(目的数据库)，展开该数据库节点，找到“可编程性”节点并展开，就可以看到“存储过程”了，在“存储过程”上点击右键，新建存储过程。然后会弹出查询分析器，在这输入创建代码就可以了。

create proc myinsert -- 创建一个存储过程，名称为myinsert  
--这里写参数，如果有的话；没有的话就空着  
as  
--这里写具体语句，可以写N个  
go--可加可不加，go的意思是另起一页，相当于下一个功能块。如果下边不写语句，可以不加！  


create proc myinsert  
        @username varchar(10),--注意这里的逗号，多个参数用逗号隔开  
        @password varchar(10),  
        @name varchar(10),  
        @usertype varchar(10),  
        @createpeople varchar(10)  
    as  
        insert into SystemUsers(UserName,PassWord,Name,UserType,CreatPeople)values(@username,@password,@name,@usertype,@createpeople)  
    go  

这个存储过程可以向SystemUsers表中插入一条记录。

以上讲的是手动建立存储过程的方法，其实完全不必这么麻烦，直接点击SQL2005左上角的“新建查询”，打开查询分析器，然后在上边的语句中加入一句话：“use JF_Charging_System”意思就是使用某个数据库，也就是在哪个数据库中建立存储过程。

例如：
复制代码
    use JF_Charging_System  
    go  
    create proc myinsert  
        @username varchar(10),  
        @password varchar(10),  
        @name varchar(10),  
        @usertype varchar(10),  
        @createpeople varchar(10)  
    as  
        insert into SystemUsers(UserName,PassWord,Name,UserType,CreatPeople)values(@username,@password,@name,@usertype,@createpeople)  
    go  

下面再来讲解如何在C#中调用存储过程。废话不过说，一段完整的代码+注释让你明白一切！这段C#代码和上边的存储过程是完全对应的。
            string strsql = "Data Source=localhost;Initial Catalog=######;Integrated Security=True";//数据库链接字符串  
            string sql = "SelectUserName";//要调用的存储过程名  
            SqlConnection conStr = new SqlConnection(strsql);//SQL数据库连接对象，以数据库链接字符串为参数  
            SqlCommand comStr = new SqlCommand(sql, conStr);//SQL语句执行对象，第一个参数是要执行的语句，第二个是数据库连接对象  
            comStr.CommandType = CommandType.StoredProcedure;//因为要使用的是存储过程，所以设置执行类型为存储过程  
            //依次设定存储过程的参数  
            comStr.Parameters.Add("@Param1", SqlDbType.Text).Value = "####";
            conStr.Open();//打开数据库连接  
            comStr.ExecuteNonQuery();//执行存储过程  
            conStr.Close();//关闭连接  

原文链接:https://www.cnblogs.com/qq458978/p/4546253.html
