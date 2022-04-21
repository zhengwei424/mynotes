# webStorage
## localStorage
localStorage.setItem(key,value) // key,value都为字符串，如果value为对象，使用JSON.stringify(object)转换为字符串
localStorage.getItem(key) // 获取key
localStorage.removeItem(key) // 删除某个key
localStorage.clear() // 清空所有

## sessionStorage



# 获取元素的宽高

一、内联样式，也就是直接把width和height写在HTML元素中的style里：

　　这种情况使用     document.getElementById('xxx').style.width即可获取宽度（高度同理）

二、写在CSS样式表中，这种情况使用 document.getElementById('xxx').style.width是获取不到的

　　应使用 document.getElementById('xxx').currentStyle.width 来获取
