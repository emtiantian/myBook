## es6 杂项
### charCodeAt 和 codePointAt   
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

+ 参考 [codePointAt的优点](http://es6.ruanyifeng.com/?search=charCodeAt&x=10&y=15#docs/string#codePointAt)
+ 参考 [字符编码相关](http://www.cnblogs.com/leesf456/p/5317574.html)
+ 参考 [详细说明codePointAt](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/charCodeAt)
