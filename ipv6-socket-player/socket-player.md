# ipv6-socket播放器

主要目的是联系数据结构

服务器发送视频 客户端接受

一步步实现

python 3.5 更改了socket类
https://docs.python.org/3.5/howto/sockets.html#socket-howto

## 单进程socket通讯
server

```python
import socket
s = socket.socket(socket.AF_INET,socket.SOCK_STREAM)
s.bind(('192.168.1.169',8088))
s.listen(1)
(conn,addr) = s.accept()
while True:
    str1 = conn.recv(1024)
    str2 = str(str1,encoding='utf-8')
    str3 = str2.upper()
    conn.send(str3.encode('utf-8'))
    if str2 == '.':
        break
conn.close()
s.close()
```
client
```python
import socket
s = socket.socket(socket.AF_INET,socket.SOCK_STREAM)
s.connect(('192.168.1.169',8088))

xx = 'aaa'
s.send(xx.encode('utf-8'))
str1 = s.recv(1024)
str2 = str(str1,encoding='utf-8')
print(str2)
s.close()
```

## socket传视频

* 客户端
* 服务器

>s等待,c发送视频查询请求,s返回查询结果,视频大小,s逐帧传输视频,c逐帧接受播放

server

```python
import socket
import cv2
import numpy

s = socket.socket(socket.AF_INET,socket.SOCK_STREAM)
s.bind(('192.168.1.169',8088))
s.listen(1)
(conn,addr) = s.accept()

# get cmd
cmd = conn.recv(1024)
cmd = str(cmd,encoding='utf-8')

# get video
capture = cv2.VideoCapture(cmd)
ret, frame = capture.read()
encode_param=[int(cv2.IMWRITE_JPEG_QUALITY),90]

if ret: # ack
    xx = 'True'
    conn.send(xx.encode('utf-8'))

while ret:
    result, imgencode = cv2.imencode('.jpg',frame)
    data = numpy.array(imgencode)
    stringData = data.tostring()

    # send len of frame
    conn.send(str(len(stringData)).ljust(16).encode('utf-8'))
    # send img
    for i in range(0,len(stringData)):
        conn.send(bytes([stringData[i]]))
    # ok    
    ans = str(conn.recv(1024),encoding='utf-8')
    if ans == 'Rec':
        print('one frame')
        ret, frame = capture.read()

    if ans == 'EOF':
        break

    
conn.close()
s.close()
```

client

```python
import socket
import cv2
import numpy
import matplotlib.pyplot as plt

def recv_size(sock,count):
    buf = b''
    while count:
        newbuf = sock.recv(count)
        if not newbuf : return None
        buf += newbuf
        count -= len(newbuf)
    return buf  

def recv_img(sock,count):
    buf = b''
    while count:
        newbuf = sock.recv(1)
        if not newbuf: return None
        buf += newbuf
        count -= len(newbuf)
    return buf

s = socket.socket(socket.AF_INET,socket.SOCK_STREAM)
s.connect(('192.168.1.169',8088))

xx = 'traffic.avi'
s.send(xx.encode('utf-8'))

ack = s.recv(1024)
ack = str(ack,encoding='utf-8')
print(ack)

if ack == 'True':
    ret = True

while ret:
    length = recv_size(s,16)

    if isinstance(length,bytes):
        stringData = recv_img(s,int(length))
        data = numpy.fromstring(stringData,dtype='uint8')
        decimg=cv2.imdecode(data,1)        
        s.send(str('Rec').encode('utf-8'))
        print('one frame')
        cv2.imshow('server',decimg)
        if cv2.waitKey(1) == 27:
            s.send(str('EOF').encode('utf-8'))
            break
    if cv2.waitKey(1) == 27:
        s.send(str('EOF').encode('utf-8'))       
        break
       


s.close()
```