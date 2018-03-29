Response 对象
ASP Response 对象用于从服务器向用户发送输出的结果。它的集合、属性和方法如下：
集合

集合	描述
Cookies	设置 cookie 的值。假如不存在，就创建 cookie ，然后设置指定的值。

属性
属性	描述
Buffer	规定是否缓存页面的输出。
CacheControl	设置代理服务器是否可以缓存由 ASP 产生的输出。
Charset	将字符集的名称追加到 Response 对象中的 content-type 报头。
ContentType	设置 Response 对象的 HTTP 内容类型。
Expires	设置页面在失效前的浏览器缓存时间（分钟）。
ExpiresAbsolute	设置浏览器上页面缓存失效的日期和时间。
IsClientConnected	指示客户端是否已从服务器断开。
Pics	向 response 报头的 PICS 标志追加值。
Status	规定由服务器返回的状态行的值。

方法
方法	描述
AddHeader	向 HTTP 响应添加新的 HTTP 报头和值。
AppendToLog	向服务器记录项目（server log entry）的末端添加字符串。
BinaryWrite	在没有任何字符转换的情况下直接向输出写数据。
Clear	清除已缓存的 HTML 输出。
End	停止处理脚本，并返回当前的结果。
Flush	立即发送已缓存的 HTML 输出。
Redirect	把用户重定向到另一个 URL。
Write	向输出写指定的字符串。

常见应用例子
1.在页面中输出数据：
Response.Write("输出内容")；

2.页面跳转并传递参数：重定向URL时传递参数，使用"?"分隔页面的链接地址和参数，有多个参数时，参数与参数之间用"&"分隔。(以下语句重定向到"test"页面，并传递两个参数parameter1和parameter2)
Response.Redirect("~/test.aspx?parameter1=value1&parameter2=value2")

3.向视图中输出图片的方法：
Using System.IO;//需要引用这个命名空间
private void ShowImage(string name)
{
    //打开图片文件，并保存在文件流中，文件位于项目"根目录\App_LocalResources"文件夹为例
    FileStream stream = new FileStream(Server.MapPath("\\App_LocalResources\\"+name), FileMode.Open);
    long fileSize = stream.Length;
    byte[] buffer = new byte[(int)fileSize];
    stream.Read(buffer, 0, (int)fileSize);
    stream.Close();
    Response.ContentType = "image/jpg";//需要标明输出类型，不然会乱码!
    Response.BinaryWrite(buffer);
}

详细说明及案例
http://www.w3school.com.cn/asp/asp_ref_response.asp
