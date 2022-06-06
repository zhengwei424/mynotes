#### gin项目目录结构


#### https证书生成及使用
```bash
$ openssl genrsa -out server.key 2048
$ openssl req -new -x509 -key server.key -out server.pem -days 3650

zhengwei@ubuntu:~/goprojects/backend/ssl$ ls
server.key  server.pem

gin中启用https：
r.RunTLS(":9090", "ssl/server.pem", "ssl/server.key")
```

#### JWT(JSON Web Token)
JWT全称JSON Web Token是一种跨域认证解决方案，属于一个开放的标准，它规定了一种Token实现方式，目前多用于前后端分离项目和OAuth3.0业务场景下
JWT原理：https://www.jb51.net/article/244767.htm
gin中使用JWT: https://www.jb51.net/article/244766.htm

#### 响应数据
##### 使用结构体返回数据
```go
结构体字段需要大写
type Response struct {
	Msg string
	Code int
}

func Test(c *gin.Context) {
  ...
  var resp = Response{"hello world", 0}
	c.SecureJSON(http.StatusOK, resp)
}
```

#### 常见问题
##### websocket报错http: connection has been hijacked
```bash
2022/04/12 12:17:23 [Recovery] 2022/04/12 - 12:17:23 panic recovered:
GET /workload/exec?namespace=default&pod=nginx&container=nginx HTTP/1.1
Host: 192.168.10.168:9090
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Cache-Control: no-cache
Connection: Upgrade
Cookie: Webstorm-c0c1c910=92dc4a0c-6cf1-44c3-a7ea-abd9d02b316d; vue_admin_template_token=admin-token; sidebarStatus=0
Origin: http://localhost:9528
Pragma: no-cache
Sec-Websocket-Extensions: permessage-deflate; client_max_window_bits
Sec-Websocket-Key: a1ZgVrSgrEUC+NRyM5hgTw==
Sec-Websocket-Version: 13
Upgrade: websocket
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/98.0.4758.102 Safari/537.36
X-Forwarded-For: 127.0.0.1
X-Forwarded-Port: 9528
X-Forwarded-Proto: ws


http: connection has been hijacked
/home/zhengwei/go/pkg/mod/github.com/gin-gonic/gin@v1.7.7/render/json.go:56 (0xf7a5c4)
        JSON.Render: panic(err)
/home/zhengwei/go/pkg/mod/github.com/gin-gonic/gin@v1.7.7/context.go:927 (0xf81b06)
        (*Context).Render: if err := r.Render(c.Writer); err != nil {
/home/zhengwei/go/pkg/mod/github.com/gin-gonic/gin@v1.7.7/context.go:970 (0xfb4426)
        (*Context).JSON: c.Render(code, render.JSON{Data: obj})
/home/zhengwei/goprojects/backend/Middlewares/recovery.go:18 (0xfb42d4)
        Recovery.func1: c.JSON(http.StatusOK, gin.H{
/home/zhengwei/go/go1.17.3/src/runtime/panic.go:1038 (0x435174)
        gopanic: done = runOpenDeferFrame(gp, d)
/home/zhengwei/go/go1.17.3/src/runtime/panic.go:221 (0x44ace6)
        panicmem: panic(memoryError)
/home/zhengwei/go/go1.17.3/src/runtime/signal_unix.go:735 (0x44acb6)
        sigpanic: panicmem()
/home/zhengwei/go/pkg/mod/k8s.io/client-go@v0.0.0-20190620085101-78d2af792bab/rest/transport.go:64 (0x8e644e)
        (*Config).TransportConfig: UserAgent:     c.UserAgent,
/home/zhengwei/go/pkg/mod/k8s.io/client-go@v0.0.0-20190620085101-78d2af792bab/rest/transport.go:31 (0x8e62f8)
        TLSConfigFor: cfg, err := config.TransportConfig()
/home/zhengwei/go/pkg/mod/k8s.io/client-go@v0.0.0-20190620085101-78d2af792bab/transport/spdy/spdy.go:37 (0xfac884)
        RoundTripperFor: tlsConfig, err := restclient.TLSConfigFor(config)
/home/zhengwei/go/pkg/mod/k8s.io/client-go@v0.0.0-20190620085101-78d2af792bab/tools/remotecommand/remotecommand.go:74 (0xfad64b)
        NewSPDYExecutor: wrapper, upgradeRoundTripper, err := spdy.RoundTripperFor(config)
/home/zhengwei/goprojects/backend/Controllers/k8s/workload/pod/pod_terminal.go:139 (0xfb3444)
        WsHandler: if executor, err = remotecommand.NewSPDYExecutor(restConf, "POST", sshReq.URL()); err != nil {
/home/zhengwei/go/pkg/mod/github.com/gin-gonic/gin@v1.7.7/context.go:168 (0xfb41ea)
        (*Context).Next: c.handlers[c.index](c)
/home/zhengwei/goprojects/backend/Middlewares/recovery.go:27 (0xfb41d4)
        Recovery: c.Next()
/home/zhengwei/go/pkg/mod/github.com/gin-gonic/gin@v1.7.7/context.go:168 (0xf89f81)
        (*Context).Next: c.handlers[c.index](c)
/home/zhengwei/go/pkg/mod/github.com/gin-gonic/gin@v1.7.7/recovery.go:99 (0xf89f6c)
        CustomRecoveryWithWriter.func1: c.Next()
/home/zhengwei/go/pkg/mod/github.com/gin-gonic/gin@v1.7.7/context.go:168 (0xf891e6)
        (*Context).Next: c.handlers[c.index](c)
/home/zhengwei/go/pkg/mod/github.com/gin-gonic/gin@v1.7.7/logger.go:241 (0xf891c9)
        LoggerWithConfig.func1: c.Next()
/home/zhengwei/go/pkg/mod/github.com/gin-gonic/gin@v1.7.7/context.go:168 (0xf88730)
        (*Context).Next: c.handlers[c.index](c)
/home/zhengwei/go/pkg/mod/github.com/gin-gonic/gin@v1.7.7/gin.go:555 (0xf88398)
        (*Engine).handleHTTPRequest: c.Next()
/home/zhengwei/go/pkg/mod/github.com/gin-gonic/gin@v1.7.7/gin.go:511 (0xf87ed1)
        (*Engine).ServeHTTP: engine.handleHTTPRequest(c)
/home/zhengwei/go/go1.17.3/src/net/http/server.go:2878 (0x76aa1a)
        serverHandler.ServeHTTP: handler.ServeHTTP(rw, req)
/home/zhengwei/go/go1.17.3/src/net/http/server.go:1929 (0x7660c7)
        (*conn).serve: serverHandler{c.server}.ServeHTTP(w, w.req)
				/home/zhengwei/go/go1.17.3/src/runtime/asm_amd64.s:1581 (0x465520)
				        goexit: BYTE    $0x90   // NOP

				[GIN-debug] [WARNING] Headers were already written. Wanted to override status code 200 with 500
				[GIN] 2022/04/12 - 12:17:23 | 500 |    60.10478ms |       127.0.0.1 | GET      "/workload/exec?namespace=default&pod=nginx&container=nginx"
```
