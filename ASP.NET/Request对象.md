Request 对象
当浏览器向服务器请求页面时，这个行为就被称为一个 request（请求）。
ASP Request 对象用于从用户那里获取信息。它的集合、属性和方法描述如下：

集合
集合	描述
ClientCertificate	包含了在客户证书中存储的字段值
Cookies	包含了 HTTP 请求中发送的所有 cookie 值
Form	包含了使用 post 方法由表单发送的所有的表单（输入）值
QueryString	包含了 HTTP 查询字符串中所有的变量值
ServerVariables	包含了所有的服务器变量值

属性
属性	描述
TotalBytes	返回在请求正文中客户端所发送的字节总数

方法
方法	描述
BinaryRead	取回作为 post 请求的一部分而从客户端送往服务器的数据，并把它存放到一个安全的数组之中。

常见应用例子
1.页面加载时请求变量的三种方法(变量名为Parameter):
protected void Page_Load(object sender, EventArgs e)
    {
      string parameter1 = Request["Parameter"];
      string parameter2 = Request.Params["Parameter"];
      string parameter3 = Request.QueryString["Parameter"];      
    }

2.获取并输出客户端浏览器信息等
public partial class Request : System.Web.UI.Page
{
    protected void Page_Load(object sender, EventArgs e)
    {
        HttpBrowserCapabilities b = Request.Browser;
        Response.Write("客户端浏览器信息:");
        Response.Write("<hr>");
        Response.Write("类型:"+b.Type+"<br>");
        Response.Write("名称:"+b.Browser +"<br>");
        Response.Write("版本:"+b.Version+"<br>");
        Response.Write("操作平台:"+b.Platform+"<br>");
        Response.Write("是否支持框架:"+b.Frames+"<br>");
        Response.Write("是否支持表格:"+b.Tables +"<br>");
        Response.Write("是否支持Cookies:"+b.Cookies +"<br>");
        Response.Write("<hr>");
        Response.Write("客户端IP地址:" + Request.UserHostAddress+"<br>");
        Response.Write("远程客户端IP地址:" + Request.ServerVariables["REMOTE_ADDR"] + "<br>");
        Response.Write("当前页面路径:" + Request.CurrentExecutionFilePath + "<br>");
        Response.Write("<hr>");
    }

详细说明及案例
http://www.w3school.com.cn/asp/asp_ref_request.asp
