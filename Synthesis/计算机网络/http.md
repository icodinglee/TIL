#### 基本概念
文档传递协议 HTTP: (HyperText Transfer Protocol)  超文本传输协议

制定文档所在地址的 URL (Uniform Resource Loader) 统一资源定位符

计算机与网络设备要相互通信，双方就必须基于相同的方法。

TCP/IP 分层

- 应用层
  应用层决定了向用户提供应用服务时通信的活动。tcp/ip协议族内预存了各类通用的应用服务。 比如FTP DNS HTTP协议就是处于这一层。

- 传输层
  传输层对于上层应用层，提供处于网络连接中的两台计算机之间的数据传输。
  在传输层有两个性质不用的协议，TCP(传输控制协议)和UDP(用户数据报协议)

- 网络层（网络互联层）
  网络层用来处理在网络上流通的数据包。数据包是网络传输的最小数据单位。 该层规定了通过怎样的传输路线到达对方计算机，并把数据包传输给对方。
 网络层就是在众多选项内选择一个传输路线。

- 数据链路层
用来处理连接网络的硬件部分。包括控制系统，硬件的设备驱动，NIC(网络适配器，即网卡)，光纤等物理可见部分（包括连接器等一切传输媒介）。硬件的范畴均在链路层的作用范围之内

浏览网页
![image.png](http://upload-images.jianshu.io/upload_images/5545478-19a58bf94195e2e4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

1. 应用层： 作为客户端，使用http协议发出一个想看某个页面的http请求。

2. 传输层： 为了传输方便，在传输层（tcp协议）把从应用层处接收到的数据(http请求报文)进行分割，并在各个报文上打上标记序号及端口号转发给网络层。

3.网络层： (ip协议)，增加作为通信目的地的mac地址后转发给链路层。

4. 接收端的服务器在链路层接收到数据，按序往上层发送，一直到应用层。当传输到应用层才算是真正接收到客户端发送过来的HTTP

#### IP TCP DNS 的关系

######   负责传输的IP
IP(intenet Protocal)网际协议位于网络层， ip协议的作用就是把各种数据包传输给对方，而要保证确实传送到对方那里，需要满足很多条件，最重要的条件是ip地址和mac地址。
- IP地址指明了节点呗分配到的地址
- mac地址是值网卡所属的固定地址
- ip地址可以和mac地址进行配对。ip地址可以变换，但是mac地址基本不会

ip间的通信依赖MAC地址，在网络上，通信的双方在非局域网时，通常是通过多台计算机和网络设备中转才能连接到对方。而在中转时，会利用下一站中转设备的mac 地址来搜索下一个中转目标。 ARP是一种用于解析地址的协议，根据通信方的ip地址就可以反查出对方的mac 地址。而这种机制被称为路由选择。

###### 确保可靠的TCP协议
tcp位于传输层，提供可靠的字节流服务。
所谓的字节流服务指的是： 为了方便传输，将大块的数据分割成以报文端为单位的数据包进行关联。
可靠的传输服务指的是： 能够把数据准确可靠的传输给对方。
tcp协议为了更容易传输大数据才把数据分割，而且tcp协议能够确认数据最终是否发送给对方。
 
为了准确无误将数据送达目标处（为了防止已失效的连接请求报文段突然又传送到了服务端，因而产生错误），tcp协议采用了三次握手策略。

示例如下：
![TCP三次握手](http://upload-images.jianshu.io/upload_images/5545478-c8fec0d67bc83fa1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

起初，服务器和客户端都为close状态，在通信开始前，双方都得创建各自的传输控制块（TCB）;
服务器创建完成之后进入listen状态，此时准备接收客户端发来的连接请求。

***第一次握手***
客户端向服务器发送连接请求报文段。该报文段的头部中SYN = 1, ACK =0, seq =x.请求发送后，客户端便进入SYN-SENT状态。
- SYN=1, SCK =1表示该报文段为连接请求报文。
- x为本次TCP通信的字节流的初始序号。tcp规定SYN=1的报文段不能有数据部分，但是要消耗掉一个序列号。

***第二次握手***
服务端收到连接请求报文段后，如果同意连接，则会发送一个应答：SYN=1，ACK=1，seq=y，ack=x+1。 
- SYN=1，ACK=1表示该报文段为连接同意的应答报文，该应答发送完成后服务端便进入SYN-RCVD状态

- seq=y表示服务端作为发送者时，发送字节流的初始序号。
- ack=x+1表示服务端希望下一个数据报发送序号从x+1开始的字节。

***第三次握手***
当客户端收到同意的应答后，还要向服务端发送一个请求报文。表示服务端发来的连接同意应答已经成功收到。
- 该报文段的头部为： ACK = 1, seq =x +1, ACK = y +1;
客户端发完这个报文段后便进入ESTABLISHED状态，服务端收到这个应答后也进入ESTABLISHED状态，此时连接的建立完成！

***为什么是三次握手而不是两次握手***
防止失效的连接请求报文被服务端接收，从而产生错误。
- 失效的连接请求： 若客户端向服务器发送的连接请求丢失，客户端等待应答超时后会再次发送连接请求。此时上一次连接请求就是失效的

如果建立连接只需要两次握手，客户端没有太大的差别，仍然需要获得服务端的应答之后才进入ESTABLISHED状态，而服务端在收到连接请求后就进入ESTABLISHED状态。此时如果网络拥堵，客户端发送的连接请求迟迟得不到相应，客户端便会超时重发，如果客户端正确接收并确认应答，双方便开始通信，通信结束之后释放连接。***此时如果那个失效的连接请求抵达了服务器，由于只有两次握手，服务器接收到请求就会变成ESTABLISHED状态，等待发送数据或主动发送数据。但是此时客户端早已经进入closed状态，服务端会一直等下取，这样就会造成服务端资源浪费***。

***通俗的理解方法***

三次握手的目的：是为了确认双方都有收发数据的能力。 

第一次： A->B，证明A有发消息的能力。

第二次： ->B && B->A，证明B有收消息，并且有发消息的能力。

第三次： A->B，证明A有收消息的能力。

二次握手达不到目的，四次多余
![数据传输](http://upload-images.jianshu.io/upload_images/5545478-b12176d3cd6b7d03.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
.
![TCP四次挥手](http://upload-images.jianshu.io/upload_images/5545478-c46f70efa798ea60.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

tcp连接的释放一共需要四步。由于tcp连接是双向的，因此在四次挥手中，前两次挥手用于断开一个方向的连接，后两次用于断开另一个方向的连接。

***第一次挥手***
若A认为数据发送完成，则它需要向B发送 连接释放请求。该请求只有报文头。
- FIN =1表示该报文段是一个连接释放请求
- seq =u, u-1是A向B发送的最后一个字节的序号。

***第二次挥手***
B接收到连接释放请求后，会通知相应的应用程序，告诉它A向B这个方向的连接已经释放。此时b进入到close-wait状态，并向A发送连接释放的应答，其报文头包含： ACK =1, seq =v, ack = u+1,
- ACK =1 : 除了TCP 连接请求报文段以外，tcp通信过程中所有数据报的ACK都为1，表示应答。
- seq = v, v-1是B向A发送的最后一个字节的序号。
- ack = u + 1 表示希望收到从U+1个字节开始的报文段，并且已经成功接收到了u个字节。

A收到该应答，进入到FIN-WAIT-2状态，等待B发送连接释放请求。
第二次挥手完成之后，A到B方向的连接已经释放，B不会再接收数据A也不会再发送数据。单数B到A的连接依然存在，B可以继续向A发送数据。

***第三次挥手***
当B向A发完所有数据后，向A发送连接释放请求，请求头：FIN=1，ACK=1，seq=w，ack=u+1。B便进入LAST-ACK状态。

***第四次挥手***
A收到释放请求后，向B发送确认应答，此时A进入TIME-WAIT状态。该状态会持续2MSL时间，若该时间段内没有B的重发请求的话，就进入CLOSED状态，撤销TCB。当B收到确认应答后，也便进入CLOSED状态，撤销TCB。

***为什么A要先进入TIME-WAIT状态，等待2MSL时间后才进入CLOSED状态？***
为了保证B能收到A的确认应答。 
若A发完确认应答后直接进入CLOSED状态，那么如果该应答丢失，B等待超时后就会重新发送连接释放请求，但此时A已经关闭了，不会作出任何响应，因此B永远无法正常关闭。

### 负责域名解析的DNS服务
  DNS服务是和  http协议一样位于应用层的协议。它提供域名到IP地址之间的解析服务。
DNS 协议提供通过域名查找 IP 地址，或逆向从 IP 地址反查域名的服务。

综合来看
![整个Http处理流程](http://upload-images.jianshu.io/upload_images/5545478-56b47ba3a58420fe.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

***http协议是无状态的***

HTTP/1.1协议自身不对请求和响应之间的通信状态进行保存。由于需要保持登录的实现，于是便引入了cookie 技术。http协议使用URI让客户端定位到资源

***告知服务器意图的 HTTP方法***
![image.png](http://upload-images.jianshu.io/upload_images/5545478-ab988047d4090025.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

GET:获取资源
GET 方法用来请求访问已被URI识别的资源。制定的资源经服务器端解析之后返回响应内容。也就是说，如果请求的资源是文本，那就保持原样返回；如果是像CGI（通用网关接口）那样的程序，则返回经过执行之后的结果。
POST:传输实体主体
PUT:传输文件
put方法用来传输文件。就像FTP协议的文件上传一样，要求再请求报文主题中包含文件内容，然后保存到请求URI值定的位置。
head: 获得报文首部
delete:删除文件
options:询问支持哪些方法
connect： 要求用隧道协议连接代理
CONNECT方法要求在与代理服务器通信时建立隧道，实现用隧道协议进行tcp通信。主要使用SSL（安全套接层）和TLS协议（传输层安全）把通信内容加密后经网络隧道传输。

#### 持久连接节省通信量
![image.png](http://upload-images.jianshu.io/upload_images/5545478-e4f23740221d088c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
使用浏览器浏览一个包含多张图片的HTML页面时候，在发送请求访问HTML页面资源的时候，也会请求该页面包含的其他资源。因此，每次的请求都会造成无谓的TCP连接建立和断开，增加通信量的开销。
![image.png](http://upload-images.jianshu.io/upload_images/5545478-321f24c78710ceb2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

为了解决上述问题，http/1.1和部分HTTP/1.0想出了持久连接，也称为HTTP keep-alive。 持久连接的特点是：只要任意一端没有明确提出断开连接，则保持tcp连接状态。

![image.png](http://upload-images.jianshu.io/upload_images/5545478-3e6ed4e99207f14b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

持久连接的好处在于减少了TCP连接的重复建立和断开所造成的额外开销，减轻了服务器端的压力。另外，减少开销的那部分时间，使得HTPP请求和响应能够更早的结束，这样WEB页面的显示速度也就相应提高了。
在HTTP/1.0中，所有连接默认都是持久连接。
持久连接使得多数请求以**管线化**方式发送成为可能。从前发送请求后需要等待并收到相应，才能发送下一请求。管线化出现后u，不用等待响应亦可发送下一个请求。

**使用cookie 进行状态管理**
HTTP是无状态协议，它不对之前发生过的请求和响应状态进行管理。也就是说，无法根据之前的状态进行本次的请求处理。
- 优点： 减少服务器的CPU及内存资源的消耗。从另一方面来说，由于HTTP协议本身非常简单，也被应用在各种场景里。
Cookie会根据从服务器端发送的响应报文内的一个叫做set-cookie的首部字段信息，通知客户端保存cookie。当下次客户端再往该服务器发送请求时，客户端会自动在请求报文中加入cooKie的值后发出。

#### HTTP报文中的HTTP信息
用于HTTP协议交互的信息叫做HTTP报文。请求端的HTTP叫做请求报文，响应端的叫响应报文。
HTTP报文本身是由多行（CR+LF）数据构成的.
http 报文大致分为报文首部和报文主体两块。
![image.png](http://upload-images.jianshu.io/upload_images/5545478-7b91dcca5eb40de4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**编码提升传输速率**
HTTP在传输数据时可以按照数据原貌直接传输，但也可以在传输过程中通过编码提升传输速率。通过在传输时编码，能有效地处理大量的访问请求。但是，编码的操作需要 计算机来完成，因此会消耗更多的CPU等资源。
报文主体和实体主体的差异
- 报文
 是http通信的基本单位，由8位组字节流组成，通过http通信传输。
- 实体
作为请求或响应的有效载荷数据被传输，其内容由实体首部和实体主体组成

HTTP报文的主体用于传输请求或响应的实体主体。

通常，报文主体等于实体主体。只有当传输中进行编码操作时，实体主体的内容发生变化，才导致它和报文主体产生差异。

**压缩传输的内容编码**
内容编码指明应用在实体内容上的编码格式，并保持实体信息原样压缩。内容编码后的实体由客户端接受并负责解码。
- gzip
- compress(unix系统的标准压缩)
- delate（zlib）
- identity（不进行编码）

**分割发送的分块传输编码**
在传输大容量数据时，通过把数据（ 实体主体）分割成多块，能够让浏览器逐步显示页面。
分块传输编码会将实体主体分成多个部分。每一块都会用十六进制标记块的大小，而实体的最后一块会使用0（CR+LF）来标记。
使用分块传输编码的实体会由接收的客户端负责解码，恢复到编码前的实体主体。
