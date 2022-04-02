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
