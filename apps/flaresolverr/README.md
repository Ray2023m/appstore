# FlareSolverr

FlareSolverr 是一个代理服务器，旨在绕过 Cloudflare 和 DDoS-GUARD 的保护机制。

## 工作原理

FlareSolverr 启动一个代理服务器，处于空闲状态，消耗极少的资源，等待用户请求。一旦收到请求，它会使用 [Selenium](https://www.selenium.dev) 配合 [undetected-chromedriver](https://github.com/ultrafunkamsterdam/undetected-chromedriver) 启动一个浏览器（Chrome），并使用用户提供的参数打开 URL。然后，它会等待 Cloudflare 验证被解决（或者超时）。一旦解决，HTML 代码和相关的 Cookies 将会返回给用户，这些 Cookies 可以用来通过其他 HTTP 客户端绕过 Cloudflare 的保护。

**注意**：Web 浏览器会消耗大量内存。如果你的机器内存较少，请避免同时发送过多请求。每个请求都会启动一个新的浏览器实例。

FlareSolverr 还支持使用持久会话。如果使用会话，确保在使用完之后关闭会话，以节省资源。