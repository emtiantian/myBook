## vue+typescript
### vue 

### typescript
1. `private num !: number` 这里`!:`表示这个变量是必须的
2. `private num ?: number` 这里`?:`表示这个变量不是是须的
3. 忽略ts某些错误 `// @ts-ignore：`
4. 

### jwt
1. 常用payload包含

            {
                "iss": "John Wu JWT",
                "iat": 1441593502,
                "exp": 1441594722,
                "aud": "www.example.com",
                "sub": "jrocket@example.com",
                "from_user": "B",
                "target_user": "A"
            }
            iss: 该JWT的签发者
            sub: 该JWT所面向的用户
            aud: 接收该JWT的一方
            exp(expires): 什么时候过期，这里是一个Unix时间戳
            iat(issued at): 在什么时候签发的
            其他
            jti token的唯一id ,防止重放攻击
            nbf (Not Before)：如果当前时间在nbf里的时间之前，则Token不被接受；一般都会留一些余地，比如几分钟；，是否使用是可选的；

2. 登录功能可以做的很完善包括， passport 实现完整的第三方登录 
> token是否被篡改（校验载荷与签名的一致性），     
> token是否过期（校验超时时间），   
> token是否被注销（<span style="color: red">部分注销不应该被实现，全部注销可以修改密钥</span>）,    
> token是否需要延长使用
>> 在响应head中authorization写入新token(比如说快要前过期时间续1s),前端读取后更新token,
>> 并在redis中创建新旧token的键值对设置30秒过期时间.过期token访问时先判断redis旧token是否有对应新token.     
>> 有现成的实现jwt-autorefresh
> 
> token是否需要强制过期（颁发时间到现在时间大于强制过期时间）

3. 缺点 token 不能实现单独销毁（全部销毁应该可以，修改秘钥）
> 单独销毁就需要保存黑名单 到数据库 相应的就破坏了token无状态的原则.

## 具体实现
1. 登录 login post账号和密码 （密码要加密）
2. 验证是否登录成功发放token 载荷一般包含如上信息 （如果要刷新则还有个刷新token refresh token）
3. 正常访问token验证 （签名是否与载荷一致，是否过期，是否强制过期，是否有权限访问对应资源，是否是重放攻击）
4. token注销
> 全部注销(修改秘钥)   
> 部分注销（不建议？）（无状态与撤销是互斥的）   
> 需要撤销的场景 （需要数据库 使用token-用户名键值对）   
>  1. 发生在退出登录（在某一端退出销毁token，或refresh token）   
>  2. 修改密码（删除对应用户下所有token，或refresh token）   
>  3. 黑名单（删除对应用户下所有token，或refresh token并登录失败）

5.  token 续1s
> access toekn 是用户凭证
> refines token （保存在数据库中？ 参考https://auth0.com/docs/tokens/refresh-token/current）




















