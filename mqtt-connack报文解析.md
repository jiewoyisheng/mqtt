确认连接请求  CONNACK
=
服务端到客户端发送的第一个报文必须是CONNACK

如果客户端在合理的时间内没有收到服务端的connack报文客户端将关闭网络连接。
###固定报头
|bit   |7-4   |3-0  |
|-----|-----|-------|
|byte1|报文类型（2）|reserved|
|-|0010|0000|
|byte2|剩余长度（2）
|-|0000|0010|

###可变报头
连接确认标志byte1
返回码 byte2


####连接确认标志
7-1必须设置为0，第零位是当前会话标志Session Present .
####当前会话

如果服务器收到cleansession 标志为1的连接，除了将connack 报文中的返回码设置为0外，必须将session Present 标志为0；

如果服务端收到一个cleansession标志为0的连接，当前会话标志取决于服务端是否已经保存了clientld对应客户端的会话状态。如果服务端已经保存了会话状态，它必须将CONNACK报文中的当前会话标志设为1。如果服务端没有保存会话状态，它必须将CONNACK报文中的当前会话状态设置为0，还需要将connack报文中的返回码设置为0。
当前会话标志使服务端和客户端在是否有已存储的会话状态上保持一致。
一旦完成了会话的初始化设置，已经保存会话状态的客户端将期望服务端维持它存储的会话状态。 如果客户端从服务端收到的当前的值与预期的不同， 客户端可以选择继续这个会话或者断开连接。 客户端可以丢弃客户端和服务端之间的会话状态， 方法是， 断开连接，将清理会话标志设置为 1，再次连接， 然后再次断开连接。

####连接返回码
|值   |   返回码响应    |    描述    |
|-----| :-------------     |  :------    |
|0|0x00连接已接受|连接已经被服务器接受|
|1|0x01连接已拒绝，不支持的洗衣版本|服务器不支持请求的mqtt协议级别|
|2|0x02连接已拒绝，不合格的客户端标识符|标识符是正确的的utf-8但是不允许使用
|3|0x03连接拒绝，服务端不可用|网络连接已经建立但是mqtt服务不可以用
|4|0x04连接拒绝，无效发用户名和密码|用户名和密码数据格式
|5|0x05连接拒绝，未授权|客户端未授权连接此服务器|
|6-255|-|保留