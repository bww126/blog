# 三、传输层安全（TLS）
## TLS协议的目标：
* 加密 （混淆数据的机制）
* 身份验证 （验证身份标识有效性的机制）
* 完整性 （检测消息是否被篡改或伪造的机制）

## TLS握手流程：
1. 首先完成一次TCP 三次握手（TLS在可靠的传输层TCP之上运行）
1. TCP建立连接后，客户端向服务器以纯文本形式发送一些规格说明，比如：TLS版本、支持的加密套件（RSA公钥加密）、一个随机数（Client-Random）、其他一些TLS选项等。
1. 服务器获取TLS协议版本已备将来通信使用，然后回复一种客户端支持的加密方式、一个随机数（Server-Random）、授信的服务器证书和非对称加密的公钥。作为可选项，服 务器也可以发送一个请求，要求客户端提供证书以及其他 TLS 扩展参数。
1. 客户端收到服务器的回复后利用服务器的公钥，加上一个新的随机数（Premaster-random），通过服务器下发的公钥和加密方式进行加密，发送给服务器。
1. 服务器收到客户端的回复后，利用已知的非对称加密方式进行解密，同时利用以上三个随机数通过一定的算法生成http链接数据传输的对称加密key发送给客户端。此后的http链接数据传输即通过对称加密方式进行加密传输。

> 注：关于对称加密的密钥生成，不同资料中有不同的说法：分别是客户端在第二次TLS握手后生成，然后在第三次握手传递给服务器；另一种是客户端与服务器通过三个随机参数协商得出，不需要相互传递；还有一种是服务器在收到Premaster-random后，生成对称加密密钥，在第四次握手传递给客户端。每种方式都可以实现密钥的安全传输，应该是在TLS协议不同版本有不同的实现方式。

## TLS性能优化
* 要最大限制提升 TCP 性能，请参考 “针对 TCP 的优化建议”;
* 把 TLS 库升级到最新版本，在此基础上构建(或重新构建)服务器;
* 启用并配置会话缓存和无状态恢复;
* 监控会话缓存的使用情况并作出相应调整;
* 在接近用户的地方完成 TLS 会话，尽量减少往返延迟;
* 配置 TLS 记录大小，使其恰好能封装在一个 TCP 段内;
* 确保证书链不会超过拥塞窗口的大小;
* 从信任链中去掉不必要的证书，减少链条层次;
* 禁用服务器的 TLS 压缩功能;
* 启用服务器对 SNI 的支持;
* 启用服务器的 OCSP 封套功能;
* 追加 HTTP 严格传输安全首部。