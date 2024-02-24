---
layout: post   	
catalog: true 	
tags:
    - python
---





# 1. C/S问题

* 服务器和客户端都有收发功能
* 这个过程中，一定要注意，收发是一一对应的，有发就要有收，并且`recv()`方法默认是阻塞的

# 2. 服务器问题

* server = socket.socket() 
* server.bind() 
* server.listen()          

以上3个方法，在整个socket期间均有效，不需要死循环

* 服务器端运行到accept()方法时，程序阻塞等待客户端的连接，当客户端连接成功时，服务器端代码继续往下执行
* 服务器端运行到 recv()方法时，程序阻塞等待接收客户端发来的信息，当接收成功时，服务器端代码继续运行
* 若程序需要一直接收请求和接收数据，accept()和recv()方法，必须死循环

参考代码

```python
# coding=gbk
import json
import socket
import time

print('began listening...')
ip_addr = ("192.168.204.131", 9990)  # 绑定本地IP地址
server = socket.socket()
server.bind(ip_addr)
server.listen(5)
try:
    while True:
        ser, addr = server.accept()
        try:
            # ser.send((bytes(json.dumps(12345678),encoding = 'gbk')))
            msg = ser.recv(1024)
            msg_data = json.loads(msg.decode('gbk'))
            print(msg_data)
            continue
        except:
            pass
            # print(123)
            # time.sleep(2)
            # msg = conn.recv(1024)
            # msg_data = json.loads(msg.decode('gbk'))
            # print(msg_data)
except:
    pass
```
# 3. 客户端问题

* connect方法

发送连接请求，只需使用一次，即可建立连接，不需要死循环

# 4. 分类

## 4.1. 阻塞

## 4.2. 非阻塞