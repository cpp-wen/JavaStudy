计算机NIO性能提升



学习NIO的过程是比较痛苦的，学习过程会有很多的问题，但是在看博客的时候，很多博客并没有提出这些问题，只是简单的介绍了NIO中基础的知识点，这对于刚入门的我充满了许多问号？小朋友你是否在学习NIO中也有许多问号？希望下面我写的这篇文章可以让你对NIO整体有一定的了解。



需求推动技术的发展，从BIO到NIO再到现在的AIO，只有了解前面模型的不足才能更好理解后面模型解决的痛点。下面我将从传统网络编程模型一步步深入。



在了解网络编程前，网络中的两个机器是如何进行数据传输的。如果学过计算机网络，你可以回答是通过网络进行数据传输，但是在java中具体是怎么样传输的，你可能就说不清楚了。java中为了在两台机器中进行数据传输引入了socket，打个比方，可以把 Socket 比作为两个城市之间的交通工具，有了它，就可以在城市之间来回穿梭了。交通工具有多种，每种交通工具也有相应的交通规则。Socket 也一样，也有多种。大部分情况下我们使用的都是基于 TCP/IP 的流套接字，它是一种稳定的通信协议。

![image-20200507220900537](C:\Users\caopeng\AppData\Roaming\Typora\typora-user-images\image-20200507220900537.png)

主机A想要跟主机B进行通讯，必须通过socket建立连接，socket连接是建立在tcp以及ip协议上，所以在创建socket通信链路的时候，客户端需要为创建的socket指定建立连接的IP地址以及服务器指定的端口来找到目标主机上运行的特定程序。同时在服务器端，服务器会创建一个socket监听指定的端口，接收远程socket的请求。当经过TCP的三次握手后，双方连接建立，在客户端为建立的socket连接分配一个指定的端口 表明客户端建立连接的端口。



建立socket连接后，需要将想要传输的数据放入socket进行网络传输，为此在java中提供了两种传输数据的数据格式，分别是基于字节操作的IO接口，以及基于字符操作的IO接口。

![image-20200507222941376](C:\Users\caopeng\AppData\Roaming\Typora\typora-user-images\image-20200507222941376.png)

为什么提供两种不同的数据格式？跟两者的使用场景有一定的关系。

各种文件存储的基础单元是字节，所以字节流可以处理任何的文件。而字符流处理的最基本的单元是Unicode单元（大小为两个字节）。如果想要处理的数据是可打印的字符，使用字符流更加便捷（字节流和字符流之间需要进行解码和编码），同时字符流默认使用缓冲区。



在java.io.*包中使用了装饰者模式和适配器模式，对这些特定的流进行增强以及转换。下面通过一个socket通讯的例子来看看以上的内容。

client客户端   server服务端

```java
public class client {
    private  static  Socket socket;
    //连接服务器IP
    private static final String Default_Ip="127.0.0.1";
    //连接服务器端口
    private static final int DEFAULT_PORT=8880;
    //停止发送消息 关键字
    private static final String QUIT_MARK="quit";

    public static void main(String[] args) {
        try {
            //创建客户端socker
            socket=new Socket(Default_Ip,DEFAULT_PORT);
            System.out.println("客户端与服务器建立连接"+socket.getPort());
            //获取socket输入流
            InputStream in=socket.getInputStream();
            //使用适配器模式将字节流转换为字符流
            BufferedReader bufferedReader=new BufferedReader(new InputStreamReader(in));
            //获取socket输出流
            OutputStream out=socket.getOutputStream();
            BufferedWriter bufferedWriter=new BufferedWriter(new OutputStreamWriter(out));
            //System.in获取 本地输入
            BufferedReader reader=new BufferedReader(new InputStreamReader(System.in));
            //循环这个线程不断监听console 输入
            while(true){
                System.out.println("请输入向服务器发送的内容");
                String msg=reader.readLine();
                bufferedWriter.write(msg+"\n");
                bufferedWriter.flush();
                System.out.println("向客户端发送"+msg);
                //读取服务端发送过来的信息
                String read=bufferedReader.readLine();
                System.out.println("服务器发送信息为"+read);
                //信息为quit 则断开socket连接
                if(QUIT_MARK.equals(read)){
                    break;
                }
            }
            //关闭资源
            reader.close();
            bufferedReader.close();
            bufferedWriter.close();
        } catch (IOException e) {
            e.printStackTrace();
        }finally {
            try {
                //关闭连接
                socket.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```

```java
public class Server {
	//监听本地端口
    private static final int DEFAULT_PORT = 8880;
    private static ServerSocket serverSocket;
    private static final String QUIT_MARK = "quit";

    public static void main(String[] args) {
        try {
            serverSocket = new ServerSocket(DEFAULT_PORT);
            System.out.println("建立连接");
            while (true) {
                System.out.println("等待连接");
                //accept 阻塞函数 获取请求连接的socket
                Socket socket = serverSocket.accept();
                System.out.println("socket.getPort() = " + socket.getPort());
                // 获取输入缓冲区
                BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(socket.getInputStream()));
                //获取输出缓冲区
                BufferedWriter bufferedWriter = new BufferedWriter(new OutputStreamWriter(socket.getOutputStream()));
                String message = null;
                while ((message = bufferedReader.readLine()) != null) {
                    //输出接收到信息  socket.getPort()获取服务端socket端口
                    System.out.println(socket.getPort() + "当前读取到的端口的信息是" + message);
                    bufferedWriter.write("回复" + socket.getPort() + message + "\n");
                    bufferedWriter.flush();
                    if (QUIT_MARK == message) {
                        System.out.println(socket.getPort() + "退出");
                        break;
                    }
                }
                bufferedReader.close();
                bufferedWriter.close();
            }
        } catch (IOException e) {
            e.printStackTrace();
        }finally {
            try {
                //关闭服务器socket
                serverSocket.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```

运行结果

| client                                                       | server                                                       |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| ![image-20200507231909791](C:\Users\caopeng\AppData\Roaming\Typora\typora-user-images\image-20200507231909791.png) | ![image-20200507231930605](C:\Users\caopeng\AppData\Roaming\Typora\typora-user-images\image-20200507231930605.png) |

通过上面的例子我们发现一个服务器只能接收一个客户端的连接，如果服务器想要接收多个请求进行处理，通过分析可以知道当有一个客户端请求到达时，服务器主线程不断 循环获取接收的信息导致外层while循环无法继续执行。想要创建多个连接最有效的办法是为每个请求的客户端都创建一个线程进行处理。于是我们修改代码为以下样子。

```java
		 while (true) {
                Socket socket = serverSocket.accept();
             	//创建一个实现runnable 接口的类处理逻辑
               	new Thread(socket).start();
                bufferedReader.close();
                bufferedWriter.close();
            }
```

不难发现如果网络中有大量的连接请求到达，超过1000个，那么服务器需要建立1000个线程来分别对不同的socket请求进行处理，这时候虽然每个线程都可以进行通信，但是性能却下降严重，因为线程的创建的销毁都需要耗费cpu的资源，同时创建一个线程需要分配给线程一定的资源一般为512kb到1M。过多线程也会占据虚拟机的空间资源。同时当线程变多时，线程进行频繁上下文切换使得真正执行时间减少。这样有的小伙伴可能会说那我使用线程池，达到线程的复用不就可以了吗？没错在多线程中使用线程池确实减少资源的消耗，同时使用线程池缓冲了过多的请求。所以在活动连接数不是特别高（小于1000）的情况下，可以考虑使用线程池，让每个连接专注与自己的IO事件，同时不用过多考虑系统的过载、限流等问题。

那么如果是淘宝旺旺这个的场景，这样海量连接 同时 不是每时每刻都发送消息的场景肯定就不能使用线程池了，因为这种场景对实时性要求比较高。同时每个线程都要实时监听端口发送的消息以及自己想要发送的内容。





ok为了解决上面的问题，java在JDK1.4的时候对io进行了重新编写取名为NIO，也称New io或者not blocking io（非阻塞io），那JDK1.4之前的就称为 OIO也称 Old io 或者blocking io（阻塞 io）。下面先聊聊阻塞和非阻塞，同步异步到时是什么。



同步与异步：

**同步：** 当用户线程发起请求后，客户端只有完成用户线程后请求后，才会将数据返回给用户。

**异步：** 当用户线程发送请求后，客户端在请求未完成前就返回用户，当真正的数据在业务处理完成后由信号或者回调的形式通知调用者。

阻塞与非阻塞：

**阻塞：** 当用户线程发起请求后，在没有得到结果前，该线程会被挂起，此时线程处于非执行状态，直到返回结果返回后，此线程才会被唤醒 继续执行。

**非阻塞：** 当用户线程发起请求后，在没有得到结果前，线程可以继续执行接下里的流程，整个过程该线程不会被挂起。

总结

同步与异步主要是对于服务端而言，若服务端按照请求信息调用顺序依次执行请求并返回则是同步方法，否则由服务端发起请求给客户端则是异步。

阻塞与非阻塞主要是对于客户端而言，若客户端发起请求后，不能继续执行下面的操作，只能等待数据返回则。线程挂起则被称为阻塞。否则继续执行后面流程称为非阻塞。

通过一个生活中的例子更加深刻了解这四者的不同组合

> 举个例子，张三煮开水。场景：张三、普通水壶（下称水壶）、水开后会响的水壶（下称响水壶）。
>
> - **同步阻塞**： 张三把水壶放到火上，在旁边等水开；
> - **同步非阻塞**：张三把水壶放到火上，去客厅看电视，时不时去厨房看看水开没有；
> - **异步阻塞**：张三把响水壶放到火上，在旁边等水开。
> - **异步非阻塞**：张三把响水壶放到火上，去客厅看电视，水壶响之前不再去看它了，响了再去拿壶。
>
> 所谓**同步异步**，是针对水壶（数据处理端）而言：**普通水壶，同步**，只能让调用者（张三）去轮询水壶开没有；**响水壶，异步**，水开后自己会发信号给张三。
>
> 所谓**阻塞非阻塞**，是针对张三而言：**在旁边等待的张三，阻塞** ， 别人叫他也干不了其他事；**看电视的张三，非阻塞**，烧水的同时还可以看电视。
>
> ——Java面试专栏的例子

| **组合方式** | **性能分析**                                                 |
| :----------- | :----------------------------------------------------------- |
| 同步阻塞     | 最常用的一种用法，使用也是最简单的，但是 I/O 性能一般很差，CPU 大部分在空闲状态。 |
| 同步非阻塞   | 提升 I/O 性能的常用手段，就是将 I/O 的阻塞改成非阻塞方式，尤其在网络 I/O 是长连接，同时传输数据也不是很多的情况下，提升性能非常有效。 这种方式通常能提升 I/O 性能，但是会增加 CPU 消耗，要考虑增加的 I/O 性能能不能补偿 CPU 的消耗，也就是系统的瓶颈是在 I/O 还是在 CPU 上。 |
| 异步阻塞     | 这种方式在分布式数据库中经常用到，例如在往一个分布式数据库中写一条记录，通常会有一份是同步阻塞的记录，而还有两至三份是备份记录会写到其它机器上，这些备份记录通常都是采用异步阻塞的方式写 I/O。 异步阻塞对网络 I/O 能够提升效率，尤其像上面这种同时写多份相同数据的情况。 |
| 异步非阻塞   | 这种组合方式用起来比较复杂，只有在一些非常复杂的分布式情况下使用，像集群之间的消息同步机制一般用这种 I/O 组合方式。如 Cassandra 的 Gossip 通信机制就是采用异步非阻塞的方式。 它适合同时要传多份相同的数据到集群中不同的机器，同时数据的传输量虽然不大，但是却非常频繁。这种网络 I/O 用这个方式性能能达到最高。 |





一开始我们举例的就是一个BIO （同步阻塞）典型案例，因为socket中的socket.accept()、socket.read()、socket.write()三个主要函数都是同步阻塞的。所以当一个连接在处理I/O的时候，系统是阻塞的，如果是单线程的话必然就挂死在那里。也就是我们的服务端只能与一个客户端建立连接。



NIO(同步非阻塞io)就是为了解决上面提及的海量的长连接场景。在学习NIO过程会涉及到许多的概念，看着客户端与服务器的代码出现的Channel ，Selector，Buffer 就迷失在代码山中，不知道如何继续学习下面。下面我提出我的一些理解，让我们一起来看NIO。

Java NIO中主要由是三个核心组件组成：分别是

- Channel （通道）
- Buffer(缓冲区)
- Selector(选择器)

其中认为最难理解的是选择器。



在NIO中改变原先OIO面向流的方式，改用面向缓冲区Buffer和Channel管道配合来处理数据。

读取和写入，只需要从channel通道中读取数据到缓冲区，或将 数据从缓冲区写入到通道中。NIO不像是OIO中那样的顺序读写，可以随意的读取Buffer中任意位置的数据。

### Buffer

在buffer中有几个变量来记录当前缓冲区的数据位置。

| **变量** | **说明**                                                |
| :------- | :------------------------------------------------------ |
| capacity | 缓冲区数组的总长度                                      |
| position | 下一个要操作的数据元素的位置                            |
| limit    | 缓冲区数组中不可操作的下一个元素的位置，limit<=capacity |
| mark     | 用于记录当前 position 的前一个位置或者默认是 0          |

通过一个简单例子来了解buffer是如何使用的。

![image-20200508183111359](C:\Users\caopeng\AppData\Roaming\Typora\typora-user-images\image-20200508183111359.png)

我们可以通过 ByteBuffer.allocate(11) 方法创建一个 11 个 byte 的数组缓冲区，初始状态如上图所示，position 的位置为 0，capacity 和 limit 默认都是数组长度。当我们写入 5 个字节时位置变化如下图所示：

![image-20200508183140674](C:\Users\caopeng\AppData\Roaming\Typora\typora-user-images\image-20200508183140674.png)

这时我们需要将缓冲区的 5 个字节数据写入 Channel 通信信道，所以我们需要调用 byteBuffer.flip() 方法，数组的状态又发生如下变化：

![image-20200508183156357](C:\Users\caopeng\AppData\Roaming\Typora\typora-user-images\image-20200508183156357.png)

这时底层操作系统就可以从缓冲区中正确读取这 5 个字节数据发送出去了。在下一次写数据之前我们在调一下 clear() 方法。缓冲区的索引状态又回到初始位置。



### Channel

上文说到channel 和buffer往往一起配合使用，主要作用是将传输的数据送入缓冲区。

java中有以下四种通道

| 类型                | 作用                                          |
| ------------------- | --------------------------------------------- |
| FileChannel         | 文件通道，用于文件的数据读写                  |
| SocketChannel       | 套接字通道，用于Socket套接字TCP连接的数据读写 |
| ServerSocketChannel | 服务器套接字通道，监听TCP连接请求             |
| DatagramChannel     | 数据通道，用于UDP协议的数据读写               |

通过一个文件复制的四种不同实现来更直观的认识channel 以及stream 和Buffer。

```java
//定义文件复制接口
public interface FileCopyInterface {
    void copyFile(File source,File target);
}
```

```java
ublic class FileCopyDemo {
    public static  final int ROUND=5;
    //比较四种文件复制性能
    public static void run(FileCopyInterface test, File source,File target){
        long sum=0l;
        for (int i = 0; i < ROUND; i++) {
            long begin=System.currentTimeMillis();
            test.copyFile(source,target);
            long end =System.currentTimeMillis();
            sum+=end-begin;
        }
        System.out.println(test.toString()+":"+sum/ROUND);
    }
    //关闭流接口 closeable 为接口，文件流均实现该接口
    public static void close(Closeable closeable){
        try {
            closeable.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public static void main(String[] args) {
        //使用stream 无缓冲区的方式 文件复制
        FileCopyInterface noBufferStreamCopy=new FileCopyInterface() {
            @Override
            public void copyFile(File source, File target) {
                FileInputStream fin=null;
                FileOutputStream fout=null;
                try {
                    fin=new FileInputStream(source);
                    fout=new FileOutputStream(target);
                    int n;
                    while(true){
                        //未读取到文件末尾
                        if(!((n=fin.read())!=-1)){
                            fout.write(n);
                        }
                    }
                } catch (FileNotFoundException e) {
                    e.printStackTrace();
                } catch (IOException e) {
                    e.printStackTrace();
                }finally {
                    close(fin);
                    close(fout);
                }
            }
        };




        //使用stream流 复制文件 添加缓冲区的方式
        FileCopyInterface BufferStreamCopy=new FileCopyInterface() {
            @Override
            public void copyFile(File source, File target) {
                BufferedInputStream fin=null;
                BufferedOutputStream fout=null;
                try {
                    fin=new BufferedInputStream(new FileInputStream(source));
                    fout=new BufferedOutputStream(new FileOutputStream(target));
                    //缓冲区大小
                    byte [] buffer=new byte[1024];
                    int len;
                    while(true){
                        try{
                            //read 函数写入buffer缓冲区
                            if(!((len=fin.read(buffer))!=-1)) break;
                            //将buffer数据写入指定文件
                            fout.write(buffer,0,len);
                        } catch (IOException e) {
                            e.printStackTrace();
                        }
                    }
                } catch (FileNotFoundException e) {
                    e.printStackTrace();
                }finally {
                    close(fin);
                    close(fout);
                }
            }
        };


        //channel 通过缓冲区
        FileCopyInterface nioBufferCopy=new FileCopyInterface() {
            @Override
            public void copyFile(File source, File target) {
                FileChannel fin=null;
                FileChannel fout=null;
                try {
                    fin=new FileInputStream(source).getChannel();
                    fout=new FileOutputStream(target).getChannel();
                    //设置缓冲区的大小
                    ByteBuffer byteBuffer= ByteBuffer.allocate(1024);
                    int len;
                    while((len=fin.read(byteBuffer))!=-1){
                        //转换缓存区的作用
                        byteBuffer.flip();
                        while(byteBuffer.hasRemaining()){
                            //将缓存中的数据写入
                            fout.write(byteBuffer);
                        }
                        //清除缓冲区文件
                        byteBuffer.clear();
                    }
                } catch (FileNotFoundException e) {
                    e.printStackTrace();
                } catch (IOException e) {
                    e.printStackTrace();
                }finally {
                    close(fin);
                    close(fout);
                }
            }
        };
        //直接使用通道转发
        FileCopyInterface copyFile =new FileCopyInterface() {
            FileChannel fin=null;
            FileChannel fout=null;
            @Override
            public void copyFile(File source, File target) {
                try {
                    fin=new FileInputStream(source).getChannel();
                    fout=new FileOutputStream(target).getChannel();
                    long transsize=0;
                    while(transsize!=fin.size()){
                            transsize+=fin.transferTo(0,fin.size(),fout);
                    }
                } catch (FileNotFoundException e) {
                    e.printStackTrace();
                } catch (IOException e) {
                    e.printStackTrace();
                }finally {
                    close(fin);
                    close(fout);
                }
            }
        };
    }
}
```

通过实现可以发现性能基本差别不是很大， 因为java 底层进行了重写。不信你也可以试试。



### Selector 

NIO中最重要也是最难理解的就是Selector，可以说理解了selector 便可以理解NIO。简单的说选择器的使命是完成io的多路复用，通过selector选择器对多个io通道的事件进行监听。简单的来说，服务器端运行的这个线程负责监听是否有客户端想要建立连接的请求过来，有则对这个连接建立的通道进行监听，以防止后续有其他的读或者写的请求继续发送过来。如果某一个时刻注册的通道里监听到有两个服务器的通道有读或者写的通知过来，那么当前线程先对已到达的事件进行处理。处理完之后继续监听端口判断是否是通知未处理。 只用一个线程监控多个通道，极大的减少了线程之间上下文切换的开销。

可供选择器监控的通道IO事件类型，包括以下四种：
（1）可读：SelectionKey.OP_READ
（2）可写：SelectionKey.OP_WRITE
（3）连接：SelectionKey.OP_CONNECT
（4）接收：SelectionKey.OP_ACCEPT

这边的IO事件指的是某个通道当前具备完成某个IO操作的状态。

在代码编写中使用选择器主要分为以下三个过程

1. 获取选择器实例
2. 将通道注册到选择器中
3. 轮询感兴趣的IO就绪事件

通过一个例子来会更好的理解NIO的模型，还是上面的那个聊天室的场景，这回使用NIO进行重写

主要思路为下图

![image-20200510204824069](https://pic-go-youdaoyun-image.oss-cn-beijing.aliyuncs.com/pic-go-youdaoyun-image/20200510204825.png)

```java
//服务端代码
public class ChatServer {
    //定义常量
    private final int PORT = 8888;
    private ByteBuffer rBuffer;
    private ByteBuffer wBuffer;
    private final int BUFFER_SIZE = 1024;
    private final String QUIT = "qiut";
    private ServerSocketChannel server = null;

    public void star() {
        try {

            //启动服务器
            server = ServerSocketChannel.open();
            //默认是阻塞式的调用 设置为非阻塞
            server.configureBlocking(false);
            server.socket().bind(new InetSocketAddress(PORT));
            System.out.println("启动服务器,监听端口" + server.socket().getLocalPort());
			//设置缓冲区
            rBuffer = ByteBuffer.allocate(BUFFER_SIZE);
            wBuffer = ByteBuffer.allocate(BUFFER_SIZE);
			//调用Selector的静态方法创建选择器
            Selector selector = Selector.open();
            //注册事件
            server.register(selector, SelectionKey.OP_ACCEPT);

            //轮询处理事件//select()返回触发的事件个数
            while (true) {
                selector.select();
                System.out.println("已有事件被触发");
                //得到触发的事件key
                Set<SelectionKey> selectionKeys = selector.selectedKeys();
                for (SelectionKey key : selectionKeys) {
                    //处理事件 handles为封装的处理事件函数
                    handles(key);
                }
                //手动清空处理过的事件,如果不clear的话,会导致事件重复处理
                selectionKeys.clear();
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    // 处理请求
    private void handles(SelectionKey key) throws IOException {
        // 处理ACCEPT事件
        if (key.isAcceptable()) {
            //获取用户请求的连接socketChannel
            SocketChannel socketChannel = server.accept();
            //默认为阻塞式的
            socketChannel.configureBlocking(false);
            //将socketchannel 注册到 选择器中 设置监听通道OP_READ事件
            socketChannel.register(key.selector(), SelectionKey.OP_READ);
            System.out.println("客户端:" + socketChannel.socket().getPort() + "已连接");

            // 处理READ事件
        } else if (key.isReadable()) {
            //读取消息
            String msg = readMsg((SocketChannel) key.channel());
            // 客户端异常
            if (msg.isEmpty()) {
                key.cancel();
                key.selector().wakeup();
            } else {
                //转发消息
                forwardMsg((SocketChannel) key.channel(), key, msg);
                //判断是否要退出
                if (QUIT.equals(msg)) {
                    key.cancel();
                    key.selector().wakeup();
                    System.out.println("客户端" + ((SocketChannel) key.channel()).socket().getPort() + "已退出");
                }
            }

        }
    }

    // 读消息
    private String readMsg(SocketChannel channel) throws IOException {

        rBuffer.clear();
        // 如果用户一直输入的话,这里就一直读取,因为只要不关channel,用户可能一直输入
        while (channel.read(rBuffer) > 0) ;
        rBuffer.flip();
        System.out.println("客户端" + channel.socket().getPort() + rBuffer.toString());
        return rBuffer.toString();

    }

    // 转发消息 将客户端信息  通过服务器转发给 其他的客户端
    private void forwardMsg(SocketChannel client, SelectionKey key, String msg) throws IOException {
        // 只要注册的key都显示
        Set<SelectionKey> keys = key.selector().keys();
        for (SelectionKey selectionKey : keys) {
            Channel channel = (Channel) selectionKey.channel();
            //排除本身监听请求的端口
            if (selectionKey.channel() instanceof ServerSocketChannel) continue;
            //判断selector是否正常 以及 当前channel是否为发送消息的channel
            if (key.isValid() && !selectionKey.channel().equals(client)) {
                wBuffer.clear();
                wBuffer.put(msg.getBytes());
                wBuffer.flip();
                //将缓冲区数据写入
                while (wBuffer.hasRemaining()) {
                    ((SocketChannel) channel).write(wBuffer);
                }
            }
        }

    }
	//主线程 
    public static void main(String[] args) {
        ChatServer chatServer = new ChatServer();
        chatServer.star();
    }

}

```

```java
//客户端代码
public class ChatClient {
    private final String IP = "127.0.0.1";
    private final int PORT = 8888;
    private final String QUIT = "quit";
    private final int BUFFER_SIZE = 1024;
    private SocketChannel client = null;
	
    private ByteBuffer rBuffer = ByteBuffer.allocate(BUFFER_SIZE);
    private ByteBuffer wBuffer = ByteBuffer.allocate(BUFFER_SIZE);


    private void star() {
        try {
            //调用静态方法创建客户端通道
            client = SocketChannel.open();
            client.configureBlocking(false);

            Selector selector = Selector.open();
            // 注册connect事件
            client.register(selector, SelectionKey.OP_CONNECT);

            //请求连接 异步
            client.connect(new InetSocketAddress(IP, PORT));

            while (true) {
                selector.select();
                for (SelectionKey key : selector.selectedKeys()) {
                    // 处理事件
                    handles(key);
                }
                selector.selectedKeys().clear();
            }

        } catch (IOException e) {
            e.printStackTrace();
        }


    }

    private void handles(SelectionKey key) throws IOException {
        SocketChannel socketChannel = (SocketChannel) key.channel();
        if (key.isConnectable()) {
            //如果返回true就说明连接就绪,可以停止连接这个动作了,false的话说明还没有连接,需要等待
            if (socketChannel.isConnectionPending()) {
                socketChannel.finishConnect();

                // 创建一个新线程 处理用户输入 
                new Thread(new UserInputHandler(this)).start();
                System.out.println("连接成功");
            }
            socketChannel.register(key.selector(), SelectionKey.OP_READ);
            // READ事件
        } else if (key.isReadable()) {
            String msg = readMsg(socketChannel);
            if (msg.isEmpty()) {
                close(key.selector());
            } else {
                System.out.println(msg);
            }
        }

    }

    // 读消息
    private String readMsg(SocketChannel socketChannel) throws IOException {
        rBuffer.clear();
        while (socketChannel.read(rBuffer) > 0) ;
        rBuffer.flip();
        return rBuffer.toString();
    }

    // 发消息
    public void send(String msg) throws IOException {
        if (msg.isEmpty()) return;
        wBuffer.clear();
        wBuffer.put(msg.getBytes());
        wBuffer.flip();
        while (wBuffer.hasRemaining()) {
            client.write(wBuffer);
        }

        // 检测用户是否退出
        if (readToQuit(msg)) {
            client.close();
        }

    }

    public static void close(Closeable closeable) {
        try {
            closeable.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    //判断是否准备退出
    public boolean readToQuit(String msg) {
        return QUIT.equals(msg);
    }

    public static void main(String[] args) {
        ChatClient chatClient = new ChatClient();
        chatClient.star();
    }
}
```

```java
//客户端
public class UserInputHandler implements Runnable {
    
    private ChatClient chatClient;

    public UserInputHandler(ChatClient chatClient) {
        this.chatClient = chatClient;
    }

    @Override
    public void run() {
        BufferedReader reader = null;
        try {
            //获取本地输入
            reader = new BufferedReader(
                    new InputStreamReader(System.in));
            //循坏等待用户输入
            while (true) {
                String msg = reader.readLine();
				//这边调用主线程的send方法
                chatClient.send(msg);
                //判断是否退出
                if (chatClient.readToQuit(msg)) {
                    break;
                }

            }
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            try {
                reader.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }

    }
}
```

如何你上面的例子不是很明白，没事，只要大概理解就可以。

在看的时候是你是否有疑问，如果nio性能真的有这么好吗，如果有1w个客户端连接服务器，那么每个请求到达的时候都要执行一定的语句进行事件的处理，这个时候是如何保证高性能的。

这块涉及到零拷贝的实现，如果展开需要很多时间，简单来说：系统io都分为两个阶段，等待就绪和操作。读操作分为等待系统可以读和真正读数据，写操作分别等待数据网卡可以写和真正可以写。需要说明的是等待就绪的阻塞是不使用CPU的，是在“空等”；而真正的读写操作的阻塞是使用CPU的，真正在”干活”，而且这个过程非常快，属于memory copy，带宽通常在1GB/s级别以上，可以理解为基本不耗时。

下图是几种常见I/O模型的对比：

![image-20200510215405880](https://pic-go-youdaoyun-image.oss-cn-beijing.aliyuncs.com/pic-go-youdaoyun-image/20200510215408.png)

同时NIO的读写函数可以立刻返回，这就给了我们不开线程利用CPU的最好机会：如果一个连接不能读写（socket.read()返回0或者socket.write()返回0），我们可以把这件事记下来，记录的方式通常是在Selector上注册标记位，然后切换到其它就绪的连接（channel）继续进行读写。







## Linux部分select/poll 和epoll区别

select/poll
进程通过将一个或者多个fd传递给select或者poll系统调用，并阻塞在select上，这样select/poll可以帮我们侦测多个fd是否处于就绪状态。select/poll是顺序扫描fd是否处于就绪状态，并且大量的套接字传给操作系统让其检查状态是非常浪费资源的（首先是用户态到内核态的大量数据的复制），因此fd的数量树有限的，也就几千个并发连接。因此它的使用受到了限制。

最大并发数量限制到1024大小，select系统监视大量连接的socket，一旦有就绪事件发生，select就会帮助我们侦测到这个事件，将这些套接字从用户空间传递到系统空间。因为涉及到大量套接字复制 且需要遍历 开销过大，所以需要将默认只能监视1024个连接。

**存在的问题:**

- 可协调fd数量和数值都不超过1024 无法实现高并发
- 使用O(n)复杂度遍历fd数组查看fd的可读写性 效率低
- 涉及大量kernel和用户态拷贝 消耗大
- 每次完成监控需要再次重新传入并且分事件传入 操作冗余



epoll
为了克服 select/poll模型的问题，epoll就出现了。epoll使用**事件驱动机制**代替顺序扫描机制，因此效率更高一些，当fd的事件就绪时，立即回调rollback函数。
select低效的原因是需要同时维护等待队列和阻塞队列，而epoll拆分了功能，想用epoll_ctl维护等待队列，epool_wait阻塞进程。因为大多数业务场景下连接的socket固定 所以等待队列不需要每次都进行修改。

功能分离使得Epoll有优化的可能性。通过内核维护一个就绪列表，引用收到数据的socket来避免遍历。

epoll的优势包括：

- 对fd数量没有限制(当然这个在poll也被解决了)
- 抛弃了bitmap数组实现了新的结构来存储多种事件类型
- 无需重复拷贝fd 随用随加 随弃随删
- 采用事件驱动避免轮询查看可读写事件

综上可知，epoll出现之后大大提高了并发量对于C10K问题轻松应对，即使后续出现了真正的异步IO，也并没有(暂时没有)撼动epoll的江湖地位，主要是因为epoll可以解决数万数十万的并发量，已经可以解决现在大部分的场景了，异步IO固然优异，但是编程难度比epoll更大，权衡之下epoll仍然富有生命力。

- **epoll的api定义**：

  ```
  
  //用户数据载体
  typedef union epoll_data {
     void    *ptr;
     int      fd;
     uint32_t u32;
     uint64_t u64;
  } epoll_data_t;
  //fd装载入内核的载体
   struct epoll_event {
       uint32_t     events;    /* Epoll events */
       epoll_data_t data;      /* User data variable */
   };
   //三板斧api
  int epoll_create(int size); 
  int epoll_ctl(int epfd, int op, int fd, struct epoll_event *event);  
  int epoll_wait(int epfd, struct epoll_event *events,
                   int maxevents, int timeout);
  ```

  - ***epoll_create\***是在内核区创建一个epoll相关的一些列结构，并且将一个句柄fd返回给用户态，后续的操作都是基于此fd的，参数size是告诉内核这个结构的元素的大小，类似于stl的vector动态数组，如果size不合适会涉及复制扩容，不过貌似4.1.2内核之后size已经没有太大用途了；
  - ***epoll_ctl\***是将fd添加/删除于epoll_create返回的epfd中，其中epoll_event是用户态和内核态交互的结构，定义了用户态关心的事件类型和触发时数据的载体epoll_data；
  - ***epoll_wait\***是阻塞等待内核返回的可读写事件，epfd还是epoll_create的返回值，events是个结构体数组指针存储epoll_event，也就是将内核返回的待处理epoll_event结构都存储下来，maxevents告诉内核本次返回的最大fd数量，这个和events指向的数组是相关的；
  - epoll_event是用户态需监控fd的代言人，后续用户程序对fd的操作都是基于此结构的；

 

- **通俗描述：**

可能上面的描述有些抽象，不过其实很好理解，举个现实中的例子：

- ***epoll_create场景***：

  大学开学第一周，你作为班长需要帮全班同学领取相关物品，你在学生处告诉工作人员，我是xx学院xx专业xx班的班长，这时工作人员确定你的身份并且给了你凭证，后面办的事情都需要用到(也就是调用epoll_create向内核申请了epfd结构，内核返回了epfd句柄给你使用)；

- ***epoll_ctl场景***：

  你拿着凭证在办事大厅开始办事，分拣办公室工作人员说班长你把所有需要办理事情的同学的学生册和需要办理的事情都记录下来吧，于是班长开始在每个学生手册单独写对应需要办的事情：

  李明需要开实验室权限、孙大熊需要办游泳卡......就这样班长一股脑写完并交给了工作人员(也就是告诉内核哪些fd需要做哪些操作)；

- ***epoll_wait场景\***：

  你拿着凭证在领取办公室门前等着，这时候广播喊xx班长你们班孙大熊的游泳卡办好了速来领取、李明实验室权限卡办好了速来取....还有同学的事情没办好，所以班长只能继续(也就是调用epoll_wait等待内核反馈的可读写事件发生并处理)；

底层实现使用红黑数和双向链表 这边吧不介绍

微信原文 连接  深入理解IO复用之epoll

原创 后端技术指南针 [后端技术指南针](javascript:void(0);) *2019-10-21*



## 反应器Reactor

### Reactor模式结构

![241052434069024.jpg](https://segmentfault.com/img/bVlyFO)

Reactor包含如下角色：

- Handle 句柄；用来标识socket连接或是打开文件；
- Synchronous Event Demultiplexer：同步事件多路分解器：由操作系统内核实现的一个函数；用于阻塞等待发生在句柄集合上的一个或多个事件；（如select/epoll；）
- Event Handler：事件处理接口
- Concrete Event HandlerA：实现应用程序所提供的特定事件处理逻辑；
- Reactor：反应器，定义一个接口，实现以下功能：
   1）供应用程序注册和删除关注的事件句柄；
   2）运行事件循环；
   3）有就绪事件到来时，分发事件到之前注册的回调函数上处理；

“反应”器名字中”反应“的由来：

> “反应”即“倒置”，“控制逆转”

具体事件处理程序不调用反应器，而是由反应器分配一个具体事件处理程序，具体事件处理程序对某个指定的事件发生做出反应；这种控制逆转又称为“好莱坞法则”（不要调用我，让我来调用你）

### 业务流程及时序图

![241052444538838.jpg](https://segmentfault.com/img/bVlyFP)

1. 应用启动，将关注的事件handle注册到Reactor中；
2. 调用Reactor，进入无限事件循环，等待注册的事件到来；
3. 事件到来，select返回，Reactor将事件分发到之前注册的回调函数中处理；

## Proactor模式

### Proactor模式结构

![241052458282851.jpg](https://segmentfault.com/img/bVlyFQ)

Proactor主动器模式包含如下角色

- Handle 句柄；用来标识socket连接或是打开文件；
- Asynchronous Operation Processor：异步操作处理器；负责执行异步操作，一般由操作系统内核实现；
- Asynchronous Operation：异步操作
- Completion Event Queue：完成事件队列；异步操作完成的结果放到队列中等待后续使用
- Proactor：主动器；为应用程序进程提供事件循环；从完成事件队列中取出异步操作的结果，分发调用相应的后续处理逻辑；
- Completion Handler：完成事件接口；一般是由回调函数组成的接口；
- Concrete Completion Handler：完成事件处理逻辑；实现接口定义特定的应用处理逻辑；

### 业务流程及时序图

![241052468598435.jpg](https://segmentfault.com/img/bVlyFR)

1. 应用程序启动，调用异步操作处理器提供的异步操作接口函数，调用之后应用程序和异步操作处理就独立运行；应用程序可以调用新的异步操作，而其它操作可以并发进行；
2. 应用程序启动Proactor主动器，进行无限的事件循环，等待完成事件到来；
3. 异步操作处理器执行异步操作，完成后将结果放入到完成事件队列；
4. 主动器从完成事件队列中取出结果，分发到相应的完成事件回调函数处理逻辑中；

## 对比两者的区别

### 主动和被动

以主动写为例：

- Reactor将handle放到select()，等待可写就绪，然后调用write()写入数据；写完处理后续逻辑；
- Proactor调用aoi_write后立刻返回，由内核负责写操作，写完后调用相应的回调函数处理后续逻辑；

可以看出，Reactor被动的等待指示事件的到来并做出反应；它有一个等待的过程，做什么都要先放入到监听事件集合中等待handler可用时再进行操作；
 Proactor直接调用异步读写操作，调用完后立刻返回；

### 实现

Reactor实现了一个被动的事件分离和分发模型，服务等待请求事件的到来，再通过不受间断的同步处理事件，从而做出反应；

Proactor实现了一个主动的事件分离和分发模型；这种设计允许多个任务并发的执行，从而提高吞吐量；并可执行耗时长的任务（各个任务间互不影响）

### 优点

Reactor实现相对简单，对于耗时短的处理场景处理高效；
 操作系统可以在多个事件源上等待，并且避免了多线程编程相关的性能开销和编程复杂性；
 事件的串行化对应用是透明的，可以顺序的同步执行而不需要加锁；
 事务分离：将与应用无关的多路分解和分配机制和与应用相关的回调函数分离开来，

Proactor性能更高，能够处理耗时长的并发场景；

### 缺点

Reactor处理耗时长的操作会造成事件分发的阻塞，影响到后续事件的处理；

Proactor实现逻辑复杂；依赖操作系统对异步的支持，目前实现了纯异步操作的操作系统少，实现优秀的如windows IOCP，但由于其windows系统用于服务器的局限性，目前应用范围较小；而Unix/Linux系统对纯异步的支持有限，应用事件驱动的主流还是通过select/epoll来实现；

### 适用场景

Reactor：同时接收多个服务请求，并且依次同步的处理它们的事件驱动程序；
 Proactor：异步接收和同时处理多个服务请求的事件驱动程序；

作者：大CC
链接：https://segmentfault.com/a/1190000002715832
来源：SegmentFault 思否

> 参考博客
>
> [深入分析 Java I/O 的工作机制](https://www.ibm.com/developerworks/cn/java/j-lo-javaio/)
>
> [Java NIO浅析](https://tech.meituan.com/2016/11/04/nio.html)
>
> 慕课专栏 高薪之路 java面试题精选
>
> Netty、Redis、Zookeeper高并发实战
>
> [浅谈NIO与零拷贝](https://juejin.im/post/5c1c532551882579520b1f47)