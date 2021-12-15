# JavaScript
## 简介
JavaScript 是脚本语言

JavaScript基础分为三个部分：
- ECMAScript：JavaScript 的语法标准。包括变量、表达式、运算符、函数、if语句、for语句等。

- DOM：Document Object Model（文档对象模型），操作页面上的元素的API。比如让盒子移动、变色、改变大小、轮播图等等。

- BOM：Browser Object Model（浏览器对象模型），操作浏览器部分功能的API。通过BOM可以操作浏览器窗口，比如弹框、控制浏览器跳转、获取浏览器分辨率等等。

通俗理解就是：ECMAScript 是 JS 的语法；DOM 和 BOM 浏览器运行环境为JS提供的API

代码书写方式：  
方式一：行内式
```html
<input type="button" value="点击" onclick="alert('hello world')" />
```
方式二：内嵌式  
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8"/>
    <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
    <title>Document</title>
</head>
<body>
    <script type="text/javascript">
        alert("hello world")
    </script>
</body>
</html>
```
方式三：引入外部JS文件  
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8"/>
    <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
    <title>Document</title>
</head>
<body>
    <script type="text/javascript" src="xxx/xxx.js"></script>
</body>
</html>
```

## 变量
#### 命名规范  
- 只能由字母(A-Z、a-z)、数字(0-9)、下划线(_)、美元符( $ )组成

- 不能以数字开头。也就是说，必须以字母(A-Z、a-z)、下划线(_)或者美元符( $ )开头。变量名中不允许出现空格。

- 不用使用 JS 语言中保留的「关键字」和「保留字」作为变量名。

- 建议用驼峰命名规则。比如getElementById、matherAndFather、aaaOrBbbAndCcc

- 变量名会区分大小写(javascript 是区分大小写的语言)。

- 变量名长度不能超过255个字符。

- 汉语可以作为变量名。但是不建议使用，因为 low
#### 定义

```javascript
var a = 100 //ES6之前定义常量和变量  

const a = 100 //ES6之后定义常量
let b = 200   //ES6之后定义变量
```  
#### 数据类型
- 基本数据类型(值类型): String字符串、Number数值、Boolean布尔值、Null空值、Undefined未定义  
- 引用数据类型(引用类型): Object对象  

注意：内置对象 Function、Array、Date、RegExp、Error等都是属于 Object 类型。也就是说，除了那五种基本数据类型之外，其他的，都称之为 Object类型

#### 栈内存和堆内存
JS中，所有变量都保存在栈内存中：
- 基本数据类型的值直接保存在栈内存中，值与值之间是相互独立存在，修改一个变量不会影响其他变量
- 引用数据类型，对象保存在堆内存中，每创建一个新对象，会在堆中开辟一块内存空间。而变量保存了对象的内存地址

#### 字符串：string  
##### 字符串定义
双引号或单引号均可  

```javascript
var s = "abcde"; // 定义一个string
console.log(s);  // 输出s
console.log(typeof s); // 获取s数据类型
console.log(s.length); // 获取s长度
```
##### 字符串的不可变性  

```javascript
// 字符串的值不可被改变，虽然看上去str的值发生了变化，其实地址变了，内存中开辟了一块新的空间
var str = "hello";
str = "nihao";
```
##### 字符串拼接
```javascript
var name = "zhengwei"
var age = 18
console.log("我是: " + name + ",age: " + age); //传统写法
console.log(`我是:${name}, age:${age}`); // ES6写法，反撇号``+模板字符串形式
console.log(`${a + b}`); // 模板字符串中可以使用表达式（即可以进行运算等）
```

##### 模板字符串``

```javascript
var name = "zhengwei";
var age = 18;
// 可以插入变量
console.log(`我是:${name}, age:${age}`);
// 可以插入表达式(即可以进行运算等）
console.log(`${a + b}`);

// 可以换行
const result = {
  name: "zhengwei",
  age: 18,
  sex: male
};
const html = `<div>
  <span>${result.name}</span>
  <span>${result.age}</span>
  <span>${result.sex}</span>
</div>`
console.log(html);

// 可以调用函数
function getName() {
  return "zhengwei";
}

console.log(`www.${getName()}.com`);

// 支持嵌套
const nameList = ["zhangsan", "lisi", "wangwu"];
function myTemplate() {
  return `<ul>
    ${nameList.map((item) => `<li>${item}</li>`).join('')}
  </ul>`;
}
Document.body.innerHTML = myTemplate();
```

#### 布尔值：boolean

布尔值有true和false两个值，主要用来做逻辑判断

#### 数值型：number

js中，所有整数和浮点数都是number类型  
最大值：Number.MAX_VALUE(1.7976931348623157e+308)  
最小值：Number.MIN_VALUE(5e-324)  
无穷大：Infinity  
无穷小：-Infinity  
非数值：NaN（Not a Number）  

```JavaScript
var a = 100;
console.log(typeof a); // 返回number

var b = 10.10;
console.log(typeof b); // 返回number

// 不能用js进行精度要求高的运算
var a = 0.1 + 0.2
console.log(a); // 返回0.30000000000000004
// 关于精度问题可以使用开源库decimal.js、 Math.js等
```

#### 空对象：null

null是引用类型，如果想定义一个引用类型，又没有想好要放什么内容的时候，可以将它初始化为null

```javascript
let a = null;
console.log(typeof a); // 返回object
```

#### undefined
声明一个变量，但是没有赋值，此时它的值就是undefined

```javascript
let name;
console.log(name); // 返回undefined
```
总结：  
- 任何数据类型和undefined运算都是NaN
- 任何值和null运算，null可看做0运算

#### 类型转换  
显示类型转换  
- toString()
- String()
- Number()
- parseInt(string)
- parseFloat(string)
- Boolean()

隐式类型转换
- isNaN()
- 自增/自减运算符：++、--
- 正号/负号：+a、-a
- 加号：+
- 运算符：-、*、/

## 运算符

## 流程控制

```javascript
// if条件判断
// prompt 请求用户输入
var height = parseFloat(prompt('请输入身高，单位是米'));
var weight = parseFloat(prompt('请输入体重，单位是公斤'));
if (条件表达式1) {
    // 条件1为真时，做的事情
} else if (条件表达式2) {
    // 条件1不满足，条件2满足时，做的事情
} else if (条件表达式3) {
    // 条件1、2不满足，条件3满足时，做的事情
} else {
    // 条件1、2、3都不满足时，做的事情
}

// switch语句
switch(表达式) {
	case 值1：
		语句体1;
		break;

	case 值2：
		语句体2;
		break;

	...
	...

	default：
		语句体 n+1;
		break;
}

// for循环
for(①初始化表达式; ②条件表达式; ④更新表达式){
	③语句...
}
for (var i = 1; i <= 100; i++) {
    console.log(i);
}

// while循环
while(条件表达式){
	语句...
}

// do...while循环
do{
	语句...
}while(条件表达式)


```

## 对象
对象属性值可以是任何的数据类型，也可以是个函数  
```javascript
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8"/>
    <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
    <title>Document</title>
</head>
<body>
<script>
    var person = {}
    person.name = "zhengwei";
    person.age = 18;
    person.gender = "male";
    console.log(person);

    var obj = new Object();
    obj.sayName = function () {
        console.log("zhengwei")
    };
    console.log(obj.sayName);
    console.log("------------");
    console.log(obj.sayName());
</script>
</body>
</html>
```

对象中的属性也可以是一个对象

```javascript
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8"/>
    <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
    <title>Document</title>
</head>
<body>
<script>
    var obj1 =  new Object();
    obj1.test = undefined;

    var obj2 = new Object();
    obj2.name = "zhengwei";

    obj1.test = obj2;

    console.log(obj1.test.name);
</script>
</body>
</html>
```  

值传递和对象传递的区别  
```javascript
// 传值
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8"/>
    <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
    <title>Document</title>
</head>
<body>
<script>
    let a = 1;
    let b = a;
    b = 2;
    console.log("a = " + a ); // 返回 a = 1
    console.log("b = " + b ); // 返回 b = 2
</script>
</body>
</html>

// 传址
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8"/>
    <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
    <title>Document</title>
</head>
<body>
<script>
    var obj1 = new Object();
    obj1.name = "张三";

    var obj2 = obj1;
    obj2.name = "李四";

    console.log("obj1.name = " + obj1.name); //返回 obj1.name  = 李四
    console.log("obj2.name = " + obj2.name); // 返回 obj2.name = 李四
</script>
</body>
</html>
```

### 内置对象

内置对象|对象说明
:--|:--
Arguments|函数参数集合
Array|数组
Boolean|布尔对象
Math|数学对象
Date|日期时间
Error|异常对象
Function|函数构造器
Number|数值对象
Object|基础对象
RegExp|正则表达式对象
String|字符串对象

#### 内置对象String
##### 查找字符串
indexOf()/lastIndexOf(): 获取字符串中指定内容的索引

```javascript
var str = "abcdef";
// indexOf() 从前往后找
console.log(str.indexOf("c")); // 返回2
// lastIndexOf() 从后往前找
console.log(str.lastIndexOf("c")); // 返回2
console.log(str.indexOf("z")); // 不存在返回-1

// 查找一个字符串中a出现的次数(利用indexOf可以指定位置查找这个功能)
var str1 = "fdsfaadafdsfadfsf";
var count = 0;
var index = str1.indexOf("a");
while (index !== -1) {
    count += 1;
    index = str1.indexOf("a", index + 1);
    console.log(index);
}
console.log(count);
```

search(): 获取字符串中指定内容的索引，参数一般为正则

```javascript
var str = "helloworld";
console.log(str.search("lo")); // 返回3
```

includes(): 字符串中是否包含指定的内容

```javascript
var str = "helloworld";
console.log(str.includes("wo")); // 返回true
```

startsWith(): 字符串是否以指定的内容开头

```javascript
var str = "helloworld";
console.log(str.startsWith("he")); // 返回true
```

endsWith(): 字符串是否以指定的内容结尾

```javascript
var str = "helloworld";
console.log(str.endsWith("ld")); // 返回true
```

chartAt(index): 获取指定位置的字符

```javascript
var str = "helloworld";
console.log(str.charAt(4)); // 返回o
```

str[index]: 获取指定位置的字符
