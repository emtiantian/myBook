# es6 杂项

## charCodeAt 和 codePointAt

        s.charAt(0) // ''
        s.charAt(1) // ''
        s.charCodeAt(0) // 55362
        s.charCodeAt(1) // 57271

        上面代码中，汉字“𠮷”（注意，这个字不是“吉祥”的“吉”）的码点是`0x20BB7`，
        UTF-16 编码为`0xD842 0xDFB7`（十进制为`55362 57271`），
        需要`4`个字节储存。对于这种`4`个字节的字符，
        JavaScript 不能正确处理，字符串长度会误判为`2`，
        而且`charAt`方法无法读取整个字符，
        `charCodeAt`方法只能分别返回前两个字节和后两个字节的值。

        ES6 提供了`codePointAt`方法，能够正确处理 4 个字节储存的字符，返回一个字符的码点。

- 参考 [codePointAt 的优点](http://es6.ruanyifeng.com/?search=charCodeAt&x=10&y=15#docs/string#codePointAt)
- 参考 [字符编码相关](http://www.cnblogs.com/leesf456/p/5317574.html)
- 参考 [详细说明 codePointAt](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/charCodeAt)

## 数组深度拷贝（）

### 对于拷贝数组不包含引用类型的时候

1. es6 数组扩展 `...` `let a = {title:"123"} ,b = {...a,view:"xxx.html"}`
2. es5 数组方法 `concat`
3. 遍历重新创建
4. 转为 json `let a = [{a:1}],let b = JSON.parse(JSON.stringify(a))`
5. 第三方扩展 lodash,lazy.js,Underscore.js

### 对于拷贝数组包含引用类型的时候

1. 遍历重新创建
2. 转为 json `let a = [{a:1}],let b = JSON.parse(JSON.stringify(a))`
3. 第三方扩展 lodash,lazy.js,Underscore.js

## 箭头函数

- 箭头函数中的 this 对象是指向调用这个函数的 this

  ```js
  // 在loadsh中使用防抖或者节流函数的时候会出现这个问题
  // 防抖函数返回的是一个包装过的函数，如果传入的参数是一个箭头函数，那么箭头函数的this会指向loadsh
  ```
