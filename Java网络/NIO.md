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







为什么计算机网络问题引出为什么io不断的再优化 ，

同步

异步

阻塞

非阻塞



BIO 

NIO

AIO

linux文件内容

文件描述符





reactor模型 redis netty源码中的使用



> 参考博客
>
> [深入分析 Java I/O 的工作机制](https://www.ibm.com/developerworks/cn/java/j-lo-javaio/)
>
> [Java NIO浅析](https://tech.meituan.com/2016/11/04/nio.html)
>
> 慕课专栏 高薪之路 java面试题精选
>
> Netty、Redis、Zookeeper高并发实战