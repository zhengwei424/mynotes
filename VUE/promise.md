#### promise异步编程
https://zhuanlan.zhihu.com/p/170133620
```javascript
// promise内的函数的两个参数resolve和reject是两个回调函数
// promise内的函数可以是具体的某个函数，也可以是匿名函数，参数都是固定为resolve和reject两个回调函数。
new Promise(function(resolve, reject) {
    // 相当于resolve函数将自己的value参数传递给then内的函数作为参数
    setTimeout(resolve, 5000, "success");
    // 相当于resolve函数将自己的value参数传递给then内的函数作为参数
    // then执行成功后不会执行catch
    setTimeout(reject, 5000, "error")
}).then(function(result) {
    // result的值等于resolve函数value参数值
    // 感觉该函数是resolve函数的具体实现
    console.log(result)
}).catch(function(err) {
    // err 的值等于reject函数的reason参数值
    // 感觉该函数是reject函数的具体实现
    console.log(err)
})
```

#### 写法
写法1：
```javascript
function test(resolve, reject) {
    var timeOut = Math.random() * 2;
    log('set timeout to: ' + timeOut + ' seconds.');
    setTimeout(function () {
        if (timeOut < 1) {
            log('call resolve()...');
            resolve('200 OK');
        }
        else {
            log('call reject()...');
            reject('timeout in ' + timeOut + ' seconds.');
        }
    }, timeOut * 1000);
}
var p1 = new Promise(test);
var p2 = p1.then(function (result) {
    console.log('成功：' + result);
});
var p3 = p2.catch(function (reason) {
    console.log('失败：' + reason);
});
```
写法2：
```javascript
function test(resolve, reject) {
    var timeOut = Math.random() * 2;
    log('set timeout to: ' + timeOut + ' seconds.');
    setTimeout(function () {
        if (timeOut < 1) {
            log('call resolve()...');
            resolve('200 OK');
        }
        else {
            log('call reject()...');
            reject('timeout in ' + timeOut + ' seconds.');
        }
    }, timeOut * 1000);
}
new Promise(test).then(function (result) {
    console.log('成功：' + result);
}).catch(function (reason) {
    console.log('失败：' + reason);
});
```
