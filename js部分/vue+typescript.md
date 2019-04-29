## vue+typescript

### vue

- directive 和 $emit 的区别 是directive 是自定义真实dom的响应事件，$emit 是定义在虚拟中
- vue 动态添加路由不能添加已有路由的子选项<span style="color: red">存疑</span>
- vue 删除路由后 实际路由还在必须刷新页面才可以
- vue scoped 模块化 css 只会影响 vue 创建的 dom 结构
  完美解决办法为使用深度作用选择器： `>>>`或`/deep/`
  也可以使用全局 css 即 css 不添加 scoped
- slot（插槽）和 provide/inject（提供/注入）一起使用实现组件间通讯？
- render 函数实质上是 template 的实现方式，在自定义组件中可以使用 render 函数实现高级功能
-

### tslint && tsconfig

`/ tslint:disable /——忽略该行以下所有代码出现的错误提示`

`/ tslint:enable /——当前ts文件重新启用tslint`

`// tslint:disable-line——忽略当前行代码出现的错误提示`

`// tslint:disable-next-line——忽略下一行代码出现的错误提示`

`"outDir": "temp/",` 修改 ts 编译文件和 source-map 文件地址

### 组件相关

- css 添加模块化 在 style 上写 scoped 即开启模块化
- 数据驱动的具体实现
- 组件边界
- 创建的 mark 时候根据一组数据创建 mark 和 infoWindow，
  在每次触发事件的时候冒泡 id,根据 id 找到对应的对象执行相关操作

### 展现相关

- icon 的使用
- 绝对 px,em,rem

### typescript

1. `private num !: number` 这里`!:`表示这个变量是必须的
2. `private num ?: number` 这里`?:`表示这个变量不是是须的
3. 忽略 ts 某些错误 `// @ts-ignore：`
4. d.ts 文件只是一个解释文件不能参与运行所以其中的枚举值不能使用到 ts 文件中
5.

### jwt

1.  常用 payload 包含

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

2.  登录功能可以做的很完善包括， passport 实现完整的第三方登录

    > token 是否被篡改（校验载荷与签名的一致性），  
    > token 是否过期（校验超时时间），  
    > token 是否被注销（<span style="color: red">部分注销不应该被实现，全部注销可以修改密钥</span>）,  
    > token 是否需要延长使用
    >
    > > 在响应 head 中 authorization 写入新 token(比如说快要前过期时间续 1s),前端读取后更新 token,
    > > 并在 redis 中创建新旧 token 的键值对设置 30 秒过期时间.过期 token 访问时先判断 redis 旧 token 是否有对应新 token.  
    > > 有现成的实现 jwt-autorefresh
    >
    > token 是否需要强制过期（颁发时间到现在时间大于强制过期时间）

3.  缺点 token 不能实现单独销毁（全部销毁应该可以，修改秘钥）
    > 单独销毁就需要保存黑名单 到数据库 相应的就破坏了 token 无状态的原则.

## 具体实现

1. 登录 login post 账号和密码 （密码要加密）
2. 验证是否登录成功发放 token 载荷一般包含如上信息 （如果要刷新则还有个刷新 token refresh token）
3. 正常访问 token 验证 （签名是否与载荷一致，是否过期，是否强制过期，是否有权限访问对应资源，是否是重放攻击）
4. token 注销

   > 全部注销(修改秘钥)  
   > 部分注销（不建议？）（无状态与撤销是互斥的）  
   > 需要撤销的场景 （需要数据库 使用 token-用户名键值对）
   >
   > 1. 发生在退出登录（在某一端退出销毁 token，或 refresh token）
   > 2. 修改密码（删除对应用户下所有 token，或 refresh token）
   > 3. 黑名单（删除对应用户下所有 token，或 refresh token 并登录失败）

5. token 续 1s
   > access toekn 是用户凭证
   > refines token （保存在数据库中？ 参考[auth0-refines token](https://auth0.com/docs/tokens/refresh-token/current)）
