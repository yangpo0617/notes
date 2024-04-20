## CDN 的工作原理
内容分发网络（CDN）通过在多个地理位置建立一个入网点（PoP）或一组 CDN 边缘服务器来工作。这个分布在不同地理位置的网络基于缓存、动态加速和边缘逻辑计算的原则工作。

### 缓存
缓存是存储相同数据的多个副本以加快数据访问的过程。在计算中，缓存的原理适用于所有类型的内存和存储管理。在 CDN 技术中，该术语是指将静态网站内容存储在网络中多个服务器上的过程。 
CDN 中的缓存的工作原理如下：

1. 来自偏远地理位置的网站访客首次从您的网站请求静态 Web 内容。
2. 请求到达您的 Web 应用程序服务器或原始服务器。原始服务器将响应发送给远程访客。同时，它还将响应的副本发送到与该访客地理上最近的 CDN POP。
3. CDN POP 服务器将副本存储为缓存文件。
4. 下次该访客或该位置的任何其他访客发出相同的请求时，将由缓存服务器而不是原始服务器发送响应。 

### 动态加速
动态加速是指通过在 Web 应用程序和客户端之间设置中间 CDN 服务器，从而加快对动态 Web 内容请求的服务器响应。缓存不适用于动态 Web 内容，因为内容因用户请求而异。CDN 服务器必须针对每个动态请求重新连接原始服务器，但它们通过优化自己与原始服务器之间的连接加速了该过程。

如果客户端通过互联网直接向 Web 服务器发送动态请求，则该请求可能会由于网络延迟而丢失或延迟。此外，可能还需要花费时间打开和关闭连接以进行安全验证。另一方面，如果附近的 CDN 服务器将请求转发到原始服务器，它们就已经建立了一个持续的、受信任的连接。
例如，以下功能可以进一步优化它们之间的连接：
- 智能路由算法
- 地理位置靠近原始服务器
- 能够处理客户端请求，缩减其大小
  
### 边缘逻辑计算
您可以对 CDN 边缘服务器进行编程，以执行简化客户端和服务器之间通信的逻辑计算。
例如，此服务器可以执行以下操作：
- 检查用户请求并修改缓存行为。
- 验证和处理不正确的用户请求。
- 在响应之前修改或优化内容。
  
Web 服务器和网络边缘之间的应用程序逻辑分布有助于开发人员减轻原始服务器的计算需求并提高网站性能。
