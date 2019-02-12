---
layout: post
title: UDP通信协议
date: 2019-1-17 21:43
category: UDP
tags: [UDP,通信协议]
description:
---



#### Qt UDP 通信

![](D:\20_Develop\00_code\00_blog\DepInjoySource\source\_img\QtUDPLink.png)

​	首先，给出[Qt UDP连接实现服务器和客户端完整代码](https://github.com/DepInjoy/BaseHouse/tree/master/Qt/GradedMessagePrompt/src/UDP)，Qt 工程中需要在pro文件中加入QT+=network引入相关模块，UDP通信是面向无连接的报文通信，通信流程如下：

1. 创建一个 socket 对象

   ```
   _udpClient = new QUdpSocket();
   ```

2. 绑定目标主机 IP 和端口（非必须，服务器必须）

   ```C++
   /*
   	_hostAddr:目标地址
   	_port：端口号
   */
   if(!_udpClient->bind(QHostAddress(_hostAddr),_port,
               QUdpSocket::ShareAddress | QUdpSocket::ReuseAddressHint)){
           qDebug() << "Error: bind addr " << _hostAddr << " port " 
               << _port << "failed !";
       }
   //用于接收数据
   connect(_udpClient,&QUdpSocket::readyRead,this,&UDPClient::_received);
   ```

3. 发送数据

   ```C++
   //如果len表示发送的字节数，如果len=-1则表示发送失败。
   int len = _udpClient->writeDatagram(bytes,hostAddr,port);
   if(len < 0){
   	qDebug() << "Error try to write " << bytes.data() << "failed !" ;
   }
   ```

4. 接收数据

   ```C++
   while (_udpClient->hasPendingDatagrams()) {
   	bytes.resize(_udpClient->pendingDatagramSize());
   	_udpClient->readDatagram(bytes.data(),bytes.size(),&chost,&cport);
   }
   ```

5. 关闭套接字

   ```C++
   if(_udpClient){
           _udpClient->close();
   }
   ```


