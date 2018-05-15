C#中SqlDataAdapter的使用

SqlDataAdapter概述
　　SqlDataAdapter是 DataSet和 SQL Server之间的桥接器，用于检索和保存数据。SqlDataAdapter通过对数据源使用适当的Transact-SQL语句映射 Fill(它可更改DataSet中的数据以匹配数据源中的数据)和 Update(它可更改数据源中的数据以匹配 DataSet中的数据)来提供这一桥接。当SqlDataAdapter填充 DataSet时，它为返回的数据创建必需的表和列(如果这些表和列尚不存在)。

我们可以通过以下三种方法来创建SqlDataAdapter对象：
　　使用方法
1、通过连接字符串和查询语句
　　string strConn="uid=账号;pwd=密码;database=数据库;server=服务器";//SQL Server链接字符串
　　strSql="SELECT * FROM 表名";
　　SqlDataAdapter da=new SqlDataAdapter(strSql,strConn);
　　DataSet ds=new DataSet();//创建DataSet实例
　　da.Fill(ds,"自定义虚拟表名");//使用DataAdapter的Fill方法(填充)，调用SELECT命令
　　这种方法有一个潜在的缺陷。假设应用程序中需要多个SqlDataAdapter对象，用这种方式来创建的话，会导致创建每个SqlDataAdapter时，都同时创建一个新的SqlConnection对象，方法二可以解决这个问题

2、通过查询语句和SqlConnection对象来创建
　　string strConn="uid=账号;pwd=密码;database=数据库;server=服务器";//SQL Server链接字符串
　　SqlConnection conn=new SqlConnection(strConn);
　　string strSql="SELECT * FROM 表名";
　　SqlDataAdapter da = new SqlDataAdapter(strSql, conn);
　　DataSet ds=new DataSet();//创建DataSet实例
　　da.Fill(ds,"自定义虚拟表名");//使用DataAdapter的Fill方法(填充)，调用SELECT命令

3、通过SqlCommand对象来创建
　　string strConn="uid=账号;pwd=密码;database=数据库;server=服务器";//SQL Server链接字符串
　　SqlConnection connSql=new SqlConnection (strConn); //Sql链接类的实例化
　　connSql.Open ();//打开数据库
　　//使用SqlDataAdapter时没有必要从Connection.open()打开，
　　//SqlDataAdapter会自动打开关闭它。
　　string strSql = "SELECT * FROM 表名"; //要执行的SQL语句
　　SqlCommand cmd=new SqlCommand(strSql,connsql);
　　SqlDataAdapter da=new SqlDataAdapter(cmd); //创建DataAdapter数据适配器实例
　　DataSet ds=new DataSet();//创建DataSet实例
　　da.Fill(ds,"自定义虚拟表名");//使用DataAdapter的Fill方法(填充)，调用SELECT命令
　　ConnSql.Close ();//关闭数据库

注意
　　如果只需要执行SQL语句或SP，就没必要用到DataAdapter ，直接用SqlCommand的Execute系列方法就可以了。sqlDataadapter的作用是实现Dataset和DB之间的桥梁：比如将对DataSet的修改更新到数据库。
　　SqlDataAdapter的UpdateCommand的执行机制是：当调用SqlDataAdapter.Update()时，检查DataSet中的所有行，然后对每一个修改过的Row执行SqlDataAdapter.UpdateCommand ，也就是说如果未修改DataSet中的数据，SqlDataAdapter.UpdateCommand不会执行。

使用要点
1、SqlDataAdapter内部通过SqlDataReader获取数据，而默认情况下SqlDataReader不能获知其查询语句对应的数据库表名，
　　所以下面的代码：
　　string strConn = "uid=账号;pwd=密码;database=数据库;server=服务器";//SQL Server链接字符串
　　strSql="SELECT * FROM 表名";
　　SqlDataAdapter da = new SqlDataAdapter(strSql,strConn);
　　DataSet ds = new DataSet();
　　da.Fill(ds);
　　会在DataSet中创建一个新的DataTable，这个新的DataTable会拥有名为CustomerID和CompanyName 列，但是DataTable对象的名称是Table，而不是我们希望的Customers。
　　这个问题，可以通过添加TableMapping来解决：
　　string strConn="uid=账号;pwd=密码;database=数据库;server=服务器";//SQL Server链接字符串
　　strSql="SELECT * FROM 表名";
　　SqlDataAdapter da=new SqlDataAdapter(strSQL,strConn);
　　da.TableMappings.Add("Table"，"Customers"); // 设置对象名称
　　DataSet ds=new DataSet();
　　da.Fill(ds);
　　其实最简洁的方法是通过使用Fill方法的重载，通过指定DataTable，像这样：
　　SqlDataAdapter.Fill(DataSet,"MyTableName");
　　这样就可以不必使用TableMappings集合。

2、在使用Fill方式时，可以指定DataTable，而不是DataSet：
　　string strConn="uid=账号;pwd=密码;database=数据库;server=服务器";//SQL Server链接字符串
　　strSql="SELECT * FROM 表名";
　　SqlDataAdapter da = new SqlDataAdapter(strSql, strConn);
　　DataTable tbl=new DataTable( );
　　da.Fill(tbl);

3、注意打开和关闭连接的处理
　　在调用SqlCommand对象执行sql命令之前，需要保证与该对象关联的SqlConnection对象时打开的，否则SqlCommand的方法执行时将引发一个异常，但是我们在上面的代码中看到，SqlDataAdapter没有这样的要求。
　　如果调用SqlDataAdapter的Fill方法，并且其SelectCommand属性的SqlConnection是关闭状态，则SqlDataAdapter会自动打开它，然后提交查询，获取结果，最后关闭连接。如果在调用Fill方法前，SqlConnection是打开的，则查询执行完毕后，SqlConnection还将是打开的，也就是说SqlDataAdapter会保证SqlConnection的状态恢复到原来的情形。
　　这有时会导致性能问题，需要注意，例如下面的代码：
　　string strConn="uid=账号;pwd=密码;database=数据库;server=服务器";//SQL Server链接字符串
　　SqlConnection conn=new SqlConnection(strConn);
　　SqlDataAdapter daCustomers,daOrders;
　　strSql="SELECT * FROM Customers";
　　daCustomers = new SqlDataAdapter(strSql, conn);
　　strSql="SELECT * FROM Orders";
　　daOrders=new SqlDataAdapter(strSql, conn);
　　DataSet ds=new DataSet();
　　daCustomers.Fill(ds,"Customers");
　　daOrders.Fill(ds,"Orders");
　　以上代码会导致连接被打开和关闭两次，在调用Fill方法时各一次。为了避免打开和关闭SqlConnection对象，在调用SqlDataAdapter对象的Fill方法之前，我们可以先打开SqlConnection对象，如果希望之后关闭连接，我们可以再调用Close方法，就像这样：
　　cn.Open();
　　daCustomers.Fill(ds,"Customers");
　　daOrders.Fill(ds,"Orders");
　　cn.Close();

4、多次调用Fill方法需要注意数据重复和有效更新数据的问题
　　string strConn="uid=账号;pwd=密码;database=数据库;server=服务器";//SQL Server链接字符串
　　strSql="SELECT * FROM Customers";
　　SqlDataAdapter da=new SqlDataAdapter(strSql, strConn);
　　DataSet ds=new DataSet();
　　da.Fill(ds,"Customers");
　　//…….
　　da.Fill(ds,"Customers");
　　我们分析上面的代码，通过两次调用Fill方法，SqlDataAdapter执行两次查询，并两次将查询结果保存到DataSet中，第一次调用在DataSet中创建了一个名为Customers的新表。第二次调用Fill方法将查询的结果追加到DataSet中的同一个表中，因此，每个客户的信息将在DataSet中出现两次!当然，如果数据库管理员对Customers表定义了主键，则SqlDataAdapter在天成DataTable时，会判断重复行，并自动丢弃掉旧的值。
　　考虑一下，假定一个特定客户在第一次调用Fill方法时，存储于数据库中，那么SqlDataAdapter会将其添加到新建的DataTable中。如果后来这个客户被删除了，那么第二次调用Fill方法时，SqlDataAdapter将不会在查询结果中找到该客户信息，但是它也不会将客户信息从DataSet中删除。这就导致了数据更新的问题。
　　所以推荐的做法是，在调用Fill方法前，先删除本地DataSet中缓存的数据!

原文链接:https://blog.csdn.net/chelen_jak/article/details/46821817
