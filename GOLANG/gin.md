# 配置go mod到Goland
```bash
go env配置
go env -w GO111MODULE=on
go env -w GOPROXY="https://goproxy.cn,direct"
这样配置之后，go get下载的东西在GOPATH/pkg/mod里


goland配置go mod
file->settings->go->go modules->envoronment->GOPROXY=https://goproxy.cn或者

项目根目录下执行
go init mod
go mod vendor  就可以将GOPATH/pkg/mod里的包拷贝到vendor目录下，使得下载的包能被import


# 编译源码为可执行文件
进入源码目录
go mod init
go mod tidy
go mod vendor
make build
```
