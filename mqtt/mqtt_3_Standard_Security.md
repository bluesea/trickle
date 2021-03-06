mqtt学习(3)
#mqtt标准——安全
`mqtt` `Security`

##引子
mqtt协议的安全性是mqtt标准的一个重要内容。本文根据v3.1.1标准文档简要说明实现和使用mqtt协议时的安全威胁和参考方案。标准原文：[mqtt-v3.1.1-os-Security](http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html\#_Toc398718111)。

##概述
 仅仅是一个参考指南，并非一个规范。<br/>
 建议实现了TLS的服务器使用8883端口（IANA服务名：secure-mqtt）。<br/>
###面临的威胁
1.设备被入侵（盗用）；<br/>
2.客户端/服务器端的静态数据可能被访问；<br/>
3.协议行为可能有副作用（例如时间差攻击timing attacks）；<br/>
4.拒绝服务攻击；<br/>
5.通信可能被拦截、修改、变向或者泄密；<br/>
6.控制报文洪泛攻击。<br/>
###安全机制
mqtt通常部署于开放的通信环境，因此需要提供以下机制：<br/>
1.用户和设备身份认证；<br/>
2.服务器资源访问控制；<br/>
3.mqtt控制报文和应用数据的完整性；<br/>
4.mqtt控制报文和应用数据的机密性。<br/>
<br/>
mqtt协议作为传输层协议，通常只关心数据传输。方案实现者需要提供相应的安全机制，通常是使用TLS。<br/>

##实现参考（Check List）
###服务器对客户端的身份认证
1.使用CONNECT报文的Username和Password域。<br/>
2.使用VPN。<br/>
3.使用TLS，验证客户端证书。<br/>
4.在应用数据中传递身份信息。<br/>
###服务器对客户端的权限控制
1.根据客户端提供的信息授权访问服务器资源，例如Username、ID、域名/IP地址、或者其他身份认证的结果。<br/>
###客户端对服务器的身份认证
mqtt协议是不对称的，协议本身并没有提供客户端对服务器进行身份认证的机制。<br/>
1.使用VPN。<br/>
2.使用TLS，验证服务器端证书。<br/>
3.在应用数据中传递身份信息。<br/>
###应用数据和控制报文的完整性
1.在应用数据中添加hash值。<br/>
2.使用TLS。<br/>
3.使用VPN。<br/>
###应用数据和控制报文的机密性
1.使用TLS。<br/>
2.使用VPN。<br/>
3.对应用数据加密（可以同时保护通信数据和静态数据的机密性）。<br/>
###不可否认
无<br/>
###检测入侵
1.使用TLS，并检查证书与域名/IP的一致性。<br/>
2.使用TLS，并检查CRL。<br/>
3.使用物理防篡改的硬件。<br/>
###检测异常行为
1.服务器监测客户端异常行为：<br/>
* 重复连接企图；<br/>
* 重复认证企图；<br/>
* 异常关闭连接；<br/>
* 话题扫描（企图发布订阅大量主题）；<br/>
* 发布不可达信息（无订阅者）；<br/>
* 连接但无数据发送的客户端等。<br/>

2.服务器需要主动断开违反安全规则的客户端。<br/>
3.服务器检测到客户端恶意行为时，动态调整阻断列表（block list），主动阻断客户端。<br/>
4.部署时考虑使用网络层协议限制客户端速率或者阻断客户端。<br/>
###其他安全考虑
1.吊销丢失或者被入侵的服务器/客户端证书。<br/>
2.回收被入侵的服务器/客户端身份信息。<br/>
3.长连接的处理：TLS重新握手；服务器强制重新连接。<br/>
4.受限设备或者受限网络环境下利用TLS会话恢复机制。<br/>
5.连接到同一个服务器的客户端相互信任。<br/>

<br/>
2015-6-2
