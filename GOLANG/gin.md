https://www.liwenzhou.com/posts/Go/go_dependency/
# go module
go module是Go1.11版本之后官方推出的版本管理工具，并且从Go1.13版本开始，go module将是Go语言默认的依赖管理工具
要启用go module支持首先要设置环境变量GO111MODULE，通过它可以开启或关闭模块支持，它有三个可选值：off、on、auto，默认值是auto。

GO111MODULE=off禁用模块支持，编译时会从GOPATH和vendor文件夹中查找包。
GO111MODULE=on启用模块支持，编译时会忽略GOPATH和vendor文件夹，只根据 go.mod下载依赖。
GO111MODULE=auto，当项目在$GOPATH/src外且项目根目录有go.mod文件时，开启模块支持。
简单来说，设置GO111MODULE=on之后就可以使用go module了，以后就没有必要在GOPATH中创建项目了，并且还能够很好的管理项目依赖的第三方包信息。

使用 go module 管理依赖后会在项目根目录下生成两个文件go.mod和go.sum。
```bash
go env配置
go env -w GO111MODULE=on
go env -w GOPROXY="https://goproxy.cn,direct"
这样配置之后，go get下载的东西在GOPATH/pkg/mod里


goland配置go mod
file->settings->go->go modules->envoronment->GOPROXY=https://goproxy.cn或者

常用的go mod 命令
go mod download    下载依赖的module到本地cache（默认为$GOPATH/pkg/mod目录）
go mod edit        编辑go.mod文件
go mod graph       打印模块依赖图
go mod init        初始化当前文件夹, 创建go.mod文件
go mod tidy        增加缺少的module，删除无用的module
go mod vendor      将依赖复制到vendor下
go mod verify      校验依赖
go mod why         解释为什么需要依赖


# 编译源码为可执行文件
进入项目目录
go mod init
go mod tidy
go mod vendor
make build
```

# godep
godep是一个通过vender模式实现的go语言的第三方依赖管理工具，类似的工具还有dep
```bash
1. 安装
go get github.com/tools/godep

2. 支持的命令
godep save     将依赖项输出并复制到Godeps.json文件中
godep go       使用保存的依赖项运行go工具
godep get      下载并安装具有指定依赖项的包
godep path     打印依赖的GOPATH路径
godep restore  在GOPATH中拉取依赖的版本
godep update   更新选定的包或go版本
godep diff     显示当前和以前保存的依赖项集之间的差异
godep version  查看版本信息

3. 使用godep
在项目目录下执行godep save命令，会在当前项目中创建Godeps和vender两个文件夹。
其中Godeps文件夹下有一个Godeps.json的文件，里面记录了项目所依赖的包信息。 vender文件夹下是项目依赖的包的源代码文件

4. vender机制
Go1.5版本之后开始支持，能够控制Go语言程序编译时依赖包搜索路径的优先级。
例如查找项目的某个依赖包，首先会在项目根目录下的vender文件夹中查找，如果没有找到就会去$GOAPTH/src目录下查找

5. godep开发流程
保证程序能够正常编译
执行godep save保存当前项目的所有第三方依赖的版本信息和代码
提交Godeps目录和vender目录到代码库。
如果要更新依赖的版本，可以直接修改Godeps.json文件中的对应项
```
# GOROOT和GOPATH
GOROOT和GOPATH都是环境变量，其中GOROOT是我们安装go开发包的路径，而从Go 1.8版本开始，Go开发包在安装完成后会为GOPATH设置一个默认目录，并且在Go1.14及之后的版本中启用了Go Module模式之后，不一定非要将代码写到GOPATH目录下，所以也就不需要我们再自己配置GOPATH了，使用默认的即可。
Go1.14版本之后，都推荐使用go mod模式来管理依赖环境了，也不再强制我们把代码必须写在GOPATH下面的src目录了，你可以在你电脑的任意位置编写go代码。（网上有些教程适用于1.11版本之前。）

# GOPROXY
默认GoPROXY配置是：GOPROXY=https://proxy.golang.org,direct，由于国内访问不到https://proxy.golang.org，所以我们需要换一个PROXY，这里推荐使用https://goproxy.io或https://goproxy.cn。

可以执行下面的命令修改GOPROXY：
```bash
go env -w GOPROXY=https://goproxy.cn,direct
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
