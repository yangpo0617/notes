原因是互联网的同源策略， 不同源的URL访问的时候会报出CORS(Cross-Origin Resource Sharing)错误。
如果两个 URL 的协议、端口（如果有指定的话）和主机都相同的话，则这两个 URL 是同源的

>现代浏览器通常将使用 file:/// 模式加载的文件的来源视为不透明的来源。这意味着，假如一个文件包括来自同一文件夹的其他文件，它们不会被认为来自同一来源，并可能引发 CORS 错误。

#### 为什么gw-tourika不需要设置CORS，但是ui-tourika确可以访问？

#### 为什么gw-tripeze需要CorsMiddleware设置access-control-allow-origin: * ? 

访问CDN的时候也需要解决CORS问题， 例如从admin.tourika.com访问Digital Ocean上的CDN资源, 需要在Digital Ocean提前设置
<img src="https://github.com/yangpo0617/notes/assets/6838219/2d116284-da63-4d76-a0dc-cd718a04766a" alt="drawing" width="400"/>
