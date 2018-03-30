SocketAsyncEventArgs使用解说

如果在.NET下写过网络通讯的同学应该感觉不陌生了,有很多刚入门的同学很多都认为这东西可以大大提高处理效能还有就是使用上很不适应.其实使用之前最好通过MSDN了解一下,该对象紧紧是Begin End模式的一个增强版本,它的主要作用主要是解决之前异步过程时创建不可复用的异步对象而产生的.主要是在高并发下节省大量对象重分配和同步相关问题,从而实现在高并发吞吐下更少的资源损耗(如果你的应用紧紧是密集通讯那性能提供相对来说是可观一点,但应用还存在其他处理那就不要想着它能有质的改变了).

SocketAsyncEventArgs(SAEA)在.net 2.0 sp1所提供开发人员主要使用它的场景分别是Accept,Send和Receive.在传统的Begin End模式中一般都通过调用Begin方法然后在回调方法中调用End来处理,其实SAEA原理差不多,只是由原来的指定的回调过程变成了完成事件,更重要的一个改变就是SAEA是可以复用的.下面详解一下SAEA的以上几种用法.

主要属性和事件

在使用SocketAsyncEventArgs进行TCP或UDP通讯的时候最常用的几个成员分别是：Buffer,BufferList,BytesTransferred,SocketError,UserToken，BytesTransferred属性，SetBuffer方法和Completed事件。

SocketAsyncEventArgs接收和发送数据都需要设置buffer,一般用SetBuffer方法或设置BufferList。通过Completed来查看完成情况，而在Completed通过SocketError和BytesTransferred结合来判断当前操作是否成功能，如在receive状态当BytesTransferred为零的时候，说明对方已经把连接断开了。

由于SocketAsyncEventArgs是异步操作，在很多情况需要的一些其他信息传递到Completed事件中，这个时候可以通过UserToken属性来解决异步信息传递的问题。

使用注意：SocketAsyncEventArgs同一时间只能进行一个操作，通过Completed来确认当前操作是否完成，如果同步完成是不会触该事件需要自己手动调用处理。

Accept
此方法是管连接接入,一般在写TCP应用的的时候需要用到(UDP下则不需要).

if(!Socket.AcceptAsync(saea))
{
    Accept_Process(Socket,saea);
}
在使用的时候一般是采用以上方法,如果是同步完成就手动调用完成处理过程,否则会触发完成事件.所以在构建saea的时候必须绑定Competed事件,也可以通过承继saea类派生OnCompleted方法处理也行.

private void Accept_Process(object sender,SocketAsyncEventArgs e)
{
       if (e.SocketError == SocketError.Success)
       {
            //成功
       }
       else
       {
            ReleaseSocket(e.AcceptSocket);
       }
       Accept();
}
可以通过判断SocketError来确定连接接入是否成功,如果方法还要处理一些有可能存在异常的逻辑,那这里最好写上try catch.防止在线程回调过程出现未知异常导致服务关闭的严重问题发生. 在使用SAEA进行Accept的时候其实也可以设置buffer,这个的主要作用是当连接接入后必须收到对方请求的数据才会触发完成事件.

Send
在Socket针对SAEA的Send操作用两个分别是SendAsync和SendToAsync,前者是基于有连接状态常用于TCP,而后者则用于无连接状态的一般发送UDP的时候使用.

if (!mSocket.SendAsync(mSendSAEA))
        {
            OnSend(this, mSendSAEA);
        }
发送方法返回一个布尔值,如果为false则为同步完成需要手动调用完整事件对应的方法;数据的发送情况同样通过SAEA的完成事件来处理.

        private void OnSend(object sender, System.Net.Sockets.SocketAsyncEventArgs e)
               {
                   if (e.SocketError == System.Net.Sockets.SocketError.Success )
                   {
                     //成功
                   }
                   else
                   {
                      // Dispose();
                   }
               }

在完成事件通过SockeError来判断发送是否成功,如果不成功那基本上就说明当前连接出现异常,可以对当前TCP连接断开.实际还有几种状态需要考虑如 IOPending,不过在发关数据的时候是单一投递则不会存在这情况 .在传统的begin,end模式中需要判断发送字节数量的情况,而在SAEA中则不需要.

Receive
接收和发送的原理基本一致.

if (!mSocket.ReceiveAsync(mReceiveSAEA))
{
      OnReceive(this, mReceiveSAEA);
}
        private void OnReceive(object sender, System.Net.Sockets.SocketAsyncEventArgs e)
        {
            if (e.SocketError == System.Net.Sockets.SocketError.Success && e.BytesTransferred > 0)
            {
                //数据接收成功


                BeginReceive();
            }
            else
            {
                Dispose();
            }
        }

注意:在Win下SocketAsyncEventArgs绑定Buffer或BufferList都是需要注意,尽可能绑定同一份byte[]的引用,如果不是会导致内部缓存区重新绑定影响性能,Mono下从源代码来看是不会有这情况发生.在一些交流中经常碰到如何用SAEA实现双功,其实网络通讯中只有输入和输出,不存在只是单向的概念,对于SAEA同一时间只能允许一个操作,所以在做应用的时候需要同时接收和发送则可以针对发和收都单独定义个SAEA即可.如果为了更好地利用做个SAEA Pool则相对来说比较好.

原文地址:https://www.cnblogs.com/smark/p/3573107.html
