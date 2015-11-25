---
layout: post
title: epoll in python
category: coding
description: 2015-11-25

---

Author:[海峰 http://weibo.com/344736086](http://weibo.com/344736086)
			[http://yanheven.github.io/](http://yanheven.github.io/)
			[http://blog.csdn.net/yanheven1](http://blog.csdn.net/yanheven1/)
		
[transfer from http://scotdoyle.com/python-epoll-howto.html](http://scotdoyle.com/python-epoll-howto.html)

```
import socket, select

EOL1 = b'


EOL2 = b'


response  = b'HTTP/1.0 200 OK
: Mon, 1 Jan 1996 01:01:01 GMT

response += b'Content-Type: text/plain
ent-Length: 13


response += b'Hello, world!'

serversocket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
serversocket.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
serversocket.bind(('0.0.0.0', 8080))
serversocket.listen(1)
serversocket.setblocking(0)
serversocket.setsockopt(socket.IPPROTO_TCP, socket.TCP_NODELAY, 1)

epoll = select.epoll()
epoll.register(serversocket.fileno(), select.EPOLLIN)

try:
   connections = {}; requests = {}; responses = {}
   while True:
      events = epoll.poll(1)
      for fileno, event in events:
         if fileno == serversocket.fileno():
            connection, address = serversocket.accept()
            connection.setblocking(0)
            epoll.register(connection.fileno(), select.EPOLLIN)
            connections[connection.fileno()] = connection
            requests[connection.fileno()] = b''
            responses[connection.fileno()] = response
         elif event & select.EPOLLIN:
            requests[fileno] += connections[fileno].recv(1024)
            if EOL1 in requests[fileno] or EOL2 in requests[fileno]:
               epoll.modify(fileno, select.EPOLLOUT)
               print('-'*40 + '
requests[fileno].decode()[:-2])
         elif event & select.EPOLLOUT:
            byteswritten = connections[fileno].send(responses[fileno])
            responses[fileno] = responses[fileno][byteswritten:]
            if len(responses[fileno]) == 0:
               epoll.modify(fileno, 0)
               connections[fileno].shutdown(socket.SHUT_RDWR)
         elif event & select.EPOLLHUP:
            epoll.unregister(fileno)
            connections[fileno].close()
            del connections[fileno]
finally:
   epoll.unregister(serversocket.fileno())
   epoll.close()
   serversocket.close()
```

