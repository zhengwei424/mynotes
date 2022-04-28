#### 常用的转义字符
```bash
1. \r : 回车,表示从当前行的最前面重新输出，覆盖之前的内容
```


#### channel
```go
package main

import (
	"errors"
	"fmt"
)

// ++++++++++++++无缓冲通道阻塞+++++++++++++++
// 1. 通道中无数据，但执行读通道
func ReadNoDataFromNoBufCh() {
	noBufCh := make(chan int)

	<-noBufCh
	fmt.Println("read from no buffer channel success")
}

// 2.通道中无数据，向通道写数据，但无协程读取
func WriteNoBufCh() {
	ch := make(chan int)
	ch <- 1
	fmt.Println("write success no block")
}

// ++++++++++++有缓冲通道阻塞+++++++++++++++++++
// 1.通道的缓存无数据，但执行读通道
func ReadNoDataFromBufCh() {
	noBufCh := make(chan int, 1)

	<-noBufCh
	fmt.Println("read from no buffer channel success")
}

// 2.通道的缓存已经占满，向通道写数据，但无协程读。
func WriteBufChButFull() {
	ch := make(chan int, 1)
	ch <- 1
	ch <- 100
	fmt.Println("write success no block")
}

// ++++++++select 无阻塞读写++++++++++++++++
// 无缓冲通道读
func ReadNoDataFromNoBufChWithSelect() {
	bufCh := make(chan int)
	if v, err := ReadWithSelect(bufCh); err != nil {
		fmt.Println(err)
	} else {
		fmt.Printf("read: %d\n", v)
	}
}

// 有缓冲通道读
func ReadNoDataFromBufChWithSelect() {
	bufCh := make(chan int, 1)
	if v, err := ReadWithSelect(bufCh); err != nil {
		fmt.Println(err)
	} else {
		fmt.Printf("read: %d\n", v)
	}
}

// select结构实现通道读
func ReadWithSelect(ch chan int) (x int, err error) {
	select {
	case x = <-ch:
		return x, nil
	default:
		return 0, errors.New("channel has no data")
	}
}

// 无缓冲通道写
func WriteNoBufChwithSelect() {
	ch := make(chan int)
	if err := WriteChWithSelect(ch); err != nil {
		fmt.Println(err)
	} else {
		fmt.Println("write success")
	}
}

// 有缓冲通道写
func WriteBufChButFullWithSelect() {
	ch := make(chan int, 1)
	ch <- 100
	if err := WriteChWithSelect(ch); err != nil {
		fmt.Println(err)
	} else {
		fmt.Println("write success")
	}
}

// select结构实现通道写
func WriteChWithSelect(ch chan int) error {
	select {
	case ch <- 1:
		return nil
	default:
		return errors.New("channel blocked, can not write")
	}
}

func main() {
	//ReadNoDataFromNoBufCh()
	//WriteNoBufCh()
	//ReadNoDataFromBufCh()
	//WriteBufChButFull()
	//ReadNoDataFromNoBufChWithSelect()
	//ReadNoDataFromBufChWithSelect()
	//WriteNoBufChwithSelect()
	WriteBufChButFullWithSelect()
}
```
