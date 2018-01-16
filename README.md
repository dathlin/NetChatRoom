# NetChatRoom
本异步聊天程序由3个项目组成，服务器，共同组件，客户端

## 版权声明
本软件著作权归Richard.Hu所有。

## 特性支持
一个基于HslCommunication实现的局域网实时聊天的项目，采用C-S架构设计实现，主要特性如下：

* 局域网聊天室支持多人在线，上限取决于服务器的电脑性能
* 支持用户名登录，支持重复的用户名登录
* 支持显示所有在线客户端的信息显示，包括在线时间，上线时间，ip地址，用户名等等
* 支持服务器主动发消息给客户端
* 支持服务器强制关闭客户端
* 支持其他人的上下线信息跟踪

## 博客地址
[http://www.cnblogs.com/dathlin/p/8097897.html](http://www.cnblogs.com/dathlin/p/8097897.html) (介绍更加详细一点)

## 界面截图
服务器
![](https://raw.githubusercontent.com/dathlin/NetChatRoom/master/img/server001.png)

客户端
![](https://raw.githubusercontent.com/dathlin/NetChatRoom/master/img/client001.png)

## 代码示例
客户端发送消息到服务器，只需要一个方法就实现数据群发操作
<pre>
<code>
net_socket_client.Send(2, "这是一个消息");
</code>
</pre>

客户端接收服务器的指令进行的操作
<pre>
<code>
        private void Net_socket_client_AcceptString(AsyncStateOne state, NetHandle customer, string data)
        {
            // 我们规定
            // 1 是系统消息，
            // 2 是用户发送的消息
            // 3 客户端在线信息
            // 4 退出指令
            // 当你的消息头种类很多以后，可以在一个统一的类中心进行规定
            if (customer == 1)
            {
                ShowSystemMsg(data);
            }
            else if(customer == 2)
            {
                ShowMsg(data);
            }
            else if(customer == 3)
            {
                ShowOnlineClient(data);
            }
            else if(customer == 4)
            {
                // 退出系统
                QuitSystem( );
            }
        }
</code>
</pre>

因为客户端的操作大部分需要和UI进行交互，所以必须使用委托在前台操作显示。

服务器端的信息处理如下：
<pre>
<code>
private void ComplexServer_AcceptString(AsyncStateOne object1, NetHandle object2, string object3)
        {
            // 我们规定
            // 1 是系统消息，
            // 2 是用户发送的消息
            // 3 客户端在线信息
            // 4 强制客户端下线
            // 当你的消息头种类很多以后，可以在一个统一的类中心进行规定
            if (object2 == 2)
            {
                // 来自客户端的消息，就只有这么一种情况
                NetMessage msg = new NetMessage()
                {
                    FromName = object1.LoginAlias,
                    Time = DateTime.Now,
                    Type = "string",
                    Content = object3,
                };

                // 群发出去
                complexServer.SendAllClients(2, JObject.FromObject(msg).ToString());
            }
        }
</code>
</pre>

整个流程就是 客户端输入信息，发送给服务端，服务器端接收到数据并群发给所有客户端显示。