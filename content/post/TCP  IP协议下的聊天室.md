---
title: Java实现TCP / IP协议下的聊天室
tags:
    - TCP/IP
    - Java
    - Demo
categories:
    - 实践项目
keywords: "Java,Demo"
date: 2019-12-20 19:34:06
top_img:
cover: https://cdn.jsdelivr.net/gh/jerryc127/CDN@latest/cover/default_bg.png
---
# Java实现TCP / IP协议下的聊天室（运用多线程）

分为了6个文件，总共分为两个大块：其中一个为Client客户端，另一个是Server服务器端，每一个大块里分别有主运行块以及发送和接受模块，然后用多线程来实现实时的收和发

<!--more-->

# Client类

```
package chatApps;
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.PrintStream;
import java.net.Socket;
import java.net.SocketTimeoutException;
import java.net.UnknownHostException;

public class Client {
    private String IP;
    private int host;

    public static void main(String[] args){
        Client client = new Client("10.120.203.132",12345);
        client.go();
    }

    public Client(String IP,int host){
        this.IP = IP;
        this.host = host;
    }

    public void go(){
        try {
            Socket client = new Socket(IP,host);
//            client.setSoTimeout(4000);
            new Thread(new CReceive(client)).start();
            new Thread(new CSend(client)).start();
        } catch (UnknownHostException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        } catch (IOException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
    }
}
```

# CReceive类

```
package chatApps;
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.PrintStream;
import java.net.Socket;
import java.net.SocketTimeoutException;
import java.net.UnknownHostException;

public class CReceive implements Runnable{
    private Socket client = null;
    public CReceive(Socket client){
        this.client = client;
    }

    @Override
    public void run() {
        // TODO Auto-generated method stub
        try {
            BufferedReader buffer = new BufferedReader(new InputStreamReader(client.getInputStream()));
            String str;
            while((str = buffer.readLine())!=null){
                System.out.println("The server:"+str);
            }
        } catch (IOException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
    }
}
```

# CSend类

```
在这里插入代码片package chatApps;
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.PrintStream;
import java.net.Socket;
import java.net.SocketTimeoutException;
import java.net.UnknownHostException;

public class CSend implements Runnable{
    private Socket client = null;
    public CSend(Socket client){
        this.client = client;
    }

    @Override
    public void run() {
        // TODO Auto-generated method stub
        BufferedReader input = new BufferedReader(new InputStreamReader(System.in));
        try {
            PrintStream output = new PrintStream(client.getOutputStream());
            String str;
            while((str = input.readLine())!=null){
                output.println(str);
            }
        } catch (IOException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
    }
}
```

# Server类

```
package chatApps;
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.PrintStream;
import java.net.ServerSocket;
import java.net.Socket;
import java.net.SocketTimeoutException;
import java.net.UnknownHostException;

public class Server {
    int host;
    public static void main(String[] args){
        Server server1 = new Server(12345);
        try {
            server1.go();
        } catch (Exception e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
    }

    public Server(int host){
        this.host = host;
    }

    public void go() throws Exception{
        Socket client = null;
        ServerSocket server = new ServerSocket(12345);
        System.out.println("Waiting for connection...");
        while(true){
            client = server.accept();
            System.out.println("The connection has been established.");
            new Thread(new SReceive(client)).start();
            new Thread(new SSend(client)).start();
        }
    }
}
```

# SReceive类

```
package chatApps;
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.PrintStream;
import java.net.ServerSocket;
import java.net.Socket;
import java.net.SocketTimeoutException;
import java.net.UnknownHostException;

public class SReceive implements Runnable{
    private Socket client = null;
    public SReceive(Socket client){
        this.client = client;
    }

    @Override
    public void run() {
        // TODO Auto-generated method stub
        try {
            BufferedReader buffer = new BufferedReader(new InputStreamReader(client.getInputStream()));
            String str;
            while((str = buffer.readLine())!=null){
                System.out.println("The client:"+str);
            }
        } catch (IOException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
    }

}
```

# SSend类

```
package chatApps;
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.PrintStream;
import java.net.ServerSocket;
import java.net.Socket;
import java.net.SocketTimeoutException;
import java.net.UnknownHostException;

public class SSend implements Runnable{
    private Socket client;
    public SSend(Socket client){
        this.client = client;
    }

    @Override
    public void run() {
        // TODO Auto-generated method stub
        BufferedReader intput = new BufferedReader(new InputStreamReader(System.in));
        try {
            PrintStream output = new PrintStream(client.getOutputStream());
            output.println("The connection has been established.");
            String str;
            while((str = intput.readLine())!=null){
                output.println(str);
            }
        } catch (IOException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
    }
}
```

**最终所能实现的效果就是可以服务器端与客户端之间相互交流，当然客户端所选的IP地址一定要是服务器所在的IP地址，并且服务器与客户端所选的端口必须相同**