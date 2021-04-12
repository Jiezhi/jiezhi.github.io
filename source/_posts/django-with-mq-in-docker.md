title: Docker中Django处理消息队列遇到的坑
author: Jiezhi.G
email: Jiezhi.G@gmail.com
premalink: fix_gravatar
thumbnailImage: 'http://qn-jiezhi.nospider.net/pet-0e24cfd3754db9af5e2f7119f1160964.jpg-thumb1'
metaAlignment: center
comments: true
date: 2018-01-05 09:35:47
url:
tags:
- Docker
- Django
- mq
- stomp
- python
categories:
- python
photos:
---

早上过来发现昨天上线的代码还是有个问题，好在很快解决了，觉得有必要做个小总结了。

<!--more-->

#### python实现mq消息接收处理

##### 框架选择

因为想不到怎么在Django里加上mq消息处理，所以就暴露出一个接口直接来调用。公司使用的是[activemq](http://activemq.apache.org/)，其支持4种协议：
> * [OpenWire](http://activemq.apache.org/openwire.html) for high performance clients in Java, C, C++, C#
> * [Stomp](http://activemq.apache.org/stomp.html) support so that clients can be written easily in C, Ruby, Perl, Python, PHP, ActionScript/Flash, Smalltalk to talk to ActiveMQ as well as any other popular Message Broker
> * [AMQP](http://activemq.apache.org/amqp.html) v1.0 support
> * [MQTT](http://activemq.apache.org/mqtt.html) v3.1 support allowing for connections in an IoT environment.
  
从中可以看到，最适合python的就是[Stomp](http://stomp.github.io/index.html)协议了。在[客户端列表](http://stomp.github.io/implementations.html)中可以找到不同实现语言对应的客户端，这里我选择了[stomp.py](https://github.com/jasonrbriggs/stomp.py)，谁让他排在搜索页面前面呢（其名称就是一种很好的SEO方式）。

###### mq客户端的实现

这里曾经遇到困扰好几天的坑：

* **协议的选择**

  之前没接触过消息队列这块，天真地以为activemq就是mq的一种协议。豆油给我一个mq服务器地址和端口号（61616）后，使用stomp.py连接总是出错，连接时可以收到mq服务器返回的消息，解析却总是出错：

  ```python
  Exception in thread StompReceiverThread-1:
  Traceback (most recent call last):
    File "/usr/local/Cellar/python3/3.6.1/Frameworks/Python.framework/Versions/3.6/lib/python3.6/threading.py", line 916, in _bootstrap_inner
      self.run()
    File "/usr/local/Cellar/python3/3.6.1/Frameworks/Python.framework/Versions/3.6/lib/python3.6/threading.py", line 864, in run
      self._target(*self._args, **self._kwargs)
    File "path/of/project/lib/python3.6/site-packages/stomp.py-4.1.19-py3.6.egg/stomp/transport.py", line 332, in __receiver_loop
      f = utils.parse_frame(frame)
    File "path/of/project/lib/python3.6/site-packages/stomp.py-4.1.19-py3.6.egg/stomp/utils.py", line 138, in parse_frame
      preamble = decode(frame[0:preamble_end])
    File "path/of/project/lib/python3.6/site-packages/stomp.py-4.1.19-py3.6.egg/stomp/backward3.py", line 29, in decode
      return byte_data.decode()
  UnicodeDecodeError: 'utf-8' codec can't decode byte 0xf0 in position 0: invalid continuation byte
  ```

  ​

  调试几天都是不行，就去提了个[issue](https://github.com/jasonrbriggs/stomp.py/issues/177)。

  后来才发现是activemq实现了四种协议的服务，而不同协议开放的端口号不一样。stomp默认端口号为**61613**，端口号一换立马可以接收到消息。连接问题解决。

  ​

* **如何加入到Django里**

  被这个问题也是困扰了好久，单独的一个脚本到底如何加入到django里。曾经想过在启动django里随即运行该脚本，却一直找不到方法。因为我想把更多的精力放到对客户分数的处理上，而不是花太多的时间来处理后端的问题。所以比较急着想把这个问题解决，然而越急越无法找到实现的办法，也舍不得花时间来思考是不是这条路是不是对的路。正所谓**我们都在不断赶路忘记了出路**。

  久久无果后，索性第一个版本就没加入消息队列的处理，回头处理数据去！

  这两天忽然想到要不就把接收消息的代码单独拎出来运行，接收到消息就直接调用本地接口就可以了。然后直接用python启动就好了，调用本地接口也OK。

  **有时候，被一个问题困扰太久就容易陷进去，不可自拔。**

* **又出问题了**

  然后把这个接收消息的脚本scp到服务器再运行又出现了2个问题：

  1. 服务器没有python3
  2. 还要安装各种依赖库（会出现各种问题）

  没办法要为这个单独的脚本制作一个docker镜像了，有点高射炮打蚊子的感觉。但是好在可以做到平台无关性，不用去解决各种依赖的问题。

  折腾一通后，可以正常启动运行了。然而天有不测风云，在本地可以正常运行的脚本，到了这里却出错了：

  ```python
  > r = requests.delete('http://0.0.0.0:8000/credit/apply-del/ED201******53')
  Traceback (most recent call last):
    File "/usr/local/lib/python3.6/site-packages/urllib3/connection.py", line 141, in _new_conn
      (self.host, self.port), self.timeout, **extra_kw)
    File "/usr/local/lib/python3.6/site-packages/urllib3/util/connection.py", line 83, in create_connection
      raise err
    File "/usr/local/lib/python3.6/site-packages/urllib3/util/connection.py", line 73, in create_connection
      sock.connect(sa)
  ConnectionRefusedError: [Errno 111] Connection refused
  ```

  悲剧（被拒）了。

  一开始以为是不是iptables配置的问题，可是没听说过iptables用来防本地访问的呀，在终端里用curl执行了一下：

  ```bash
  $ curl -X "DELETE" "http://0.0.0.0:8000/credit/apply-del/ED201******53"
  {"detail":"Not found.","status_code":404}
  ```

  没问题（忽略404），也就是不是防火墙的问题之类的。又怀疑是requests库的问题，又进这个镜像里用python3自带的urllib.request执行也是被拒。正想着难道非要执行curl命令才行？不合常理呀。

  这时候机智的我灵光一现，难不成是在docker里运行的问题？docker就算是一个轻量级的虚拟机了，网络应该默认是**bridge**形式的。

  嗯，改为**--net=host**，搞定！

### 最后附上处理消息队列的脚本(关键地方已经打码)：

  ```python
  #!/usr/bin/env python
  """
  Created on 04/12/2017

  @author: 'Jiezhi.G@gmail.com'

  Reference:
  """
  from json import JSONDecodeError

  import stomp
  import time
  import json
  import requests
  import logging

  logging.basicConfig(filename='credit_message.log', level=logging.DEBUG, format='%(asctime)s %(message)s')


  class MyListener(stomp.ConnectionListener):
      def on_error(self, headers, body):
          logging.error('received an error "%s"' % body)
          print('received an error "%s"' % body)
    
      def on_message(self, headers, body):
          logging.info('\n')
          logging.info('received a message "%s"' % body)
          print('received a message "%s"' % body)
          # print(body['apply_id'])
          try:
              data = json.loads(body)
              if data['applyId']:
                  delete_url = 'http://0.0.0.0:8000/credit/apply-del/' + data['applyId']
                  r = requests.delete(delete_url)
                  print(r.status_code)
                  print(r.content)
          except KeyError:
              logging.error('KeyError process error: %s' % body)
          except JSONDecodeError:
              logging.error('JSONDecodeError process error: %s' % body)
              print('error:', body)
    
          logging.info('-' * 80)
          logging.info('\n')
          print('-' * 80)
    
      def on_connected(self, headers, body):
          logging.info('Connected')
          print('Connected')
    
      def on_connecting(self, host_and_port):
          logging.info('Connecting')
          print('connecting', host_and_port)
    
      def on_disconnected(self):
          logging.info('disconnected')
          print('disconnected')
    
      def on_heartbeat(self):
          logging.info('heartbeat')
          print('heartbeat')


  def connect_mq_server():
      # conn = stomp.Connection([('127.0.0.1', 61616)])
      # conn = stomp.Connection11([('192.168.*.*', 61613)])
      conn = stomp.Connection11([('*.1.*.2', 61613)])
      conn.set_listener('', MyListener())
      conn.start()
    
      conn.connect('admin', 'password', wait=True)
      conn.subscribe(destination='queue.bc.rgb.cs.commit.risk',
                     id='1',
                     ack='auto')
    
      # conn.send(body='Hello world', destination='/queue/test')
    
      while True:
          time.sleep(5)
      # conn.disconnect()


  if __name__ == '__main__':
      connect_mq_server()
  ```

  以及启动脚本：

  ```bash
  #!/bin/bash

  docker run \
             -d \
             --name credit_mq \
             --net=host \
             -v /home/docker/credit_message.log:/app/credit_message.log \
             credit_mq:v1 \
             python handle_message_queue.py
  ```

  算了Dockerfile也放出来吧：

  ```dockerfile
  FROM python:3

  RUN mkdir /app
  WORKDIR /app

  COPY requirements.txt /app/requirements.txt
  RUN pip install -r requirements.txt --trusted-host pypi.douban.com -i http://pypi.douban.com/simple

  COPY . /app
  CMD python handle_message_queue.py
  ```
