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

# ubuntu中goland无法输入中文的问题
```
# 1.进入goland安装目录中bin目录下，在goland.sh脚本中添加
根据自己当前设置的输入法管理工具设置，如ibus或者fcitx等
export XMODIFIERS="@im=fcitx"
export GTK_IM_MODULE="fcitx"
export QT_IM_MODULE="fcitx"

# 2.启动goland，继续设置
help->Edit Custom VM Options
添加-Drecreate.x11.input.method=true
重启IDE

# 3.解决ubuntu安装搜狗输入法之后，输入栏一直固定在左下角问题
搜狗输入法设置——》外观——》取消勾选“候选窗口跟随光标”
```
