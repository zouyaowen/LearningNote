## 网络抓包工具Wireshark学习笔记

### 过滤条件

#### 显示主机IP交互的HTTP请求

> ip.addr==172.16.188.209 and http
>
> 协议条件与其他条件构成使用and

#### 显示特定主机之间的交互请求

> ip.src==172.16.188.209&ip.dst==172.16.188.16
>
> ip.src==172.16.188.209||ip.src==172.16.188.16
>
> (ip.src==172.16.188.209||ip.src==172.16.188.16) and http
>
> (ip.addr==172.16.188.209||ip.addr==172.16.188.16) and http  
>
> (ip.addr==172.16.188.209 & ip.addr==172.16.188.16) and http 满足要求
>
> 不同的条件使用&

#### 协议

>  如果没有特别指明是什么协议，则默认使用所有支持的协议

#### Direction

> 可能的值: src, dst, src and dst, src or dst
>
> 如果没有特别指明来源或目的地，则默认使用 “src or dst” 作为关键字。
>
> 例如，`host 10.2.2.2`与`src or dst host 10.2.2.2`是一样的

#### Host(s)

可能的值： `net`, `port`, `host`, `portrange`

如果没有指定此值，则默认使用"host"关键字。

例如，src 10.1.1.1`与`src host 10.1.1.1`相同。

#### Logical Operations（逻辑运算）

可能的值：`not`, `and`, `or` 

<http://www.imooc.com/article/74915?block_id=tuijian_wz> 