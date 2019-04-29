## es6 相关记录

### 私有变量和私有方法

es6 没有在 class 中实现私有关键字 private

私有属性的特征

- class 内部不同方法间可以使用，因此 this 要指向实例化对象（必须）
- 不能被外部访问，因此实例化对象\$person.name 既不能获得值，也不能设定值（必须）
- 不能被继承，因此 extends 后子类不具备该属性，但理论上可以重新手工添加（必须）
- 约定为前面有下划线的 this.\_name 形式（备选）

暂时需要自己实现,一般有 2 种实现方法

1.  使用 weakmap

        const _mytimeOpts = new WeakMap()
        class mytime {

          constructor () {
            let opts = {'startTime': '', 'endTime': ''}
            _mytimeOpts.set(this, opts)
          }

          strat () {
            let startTime = Date.now()
            _mytimeOpts.get(this).startTime = startTime
          }

          end () {
            let endTime = Date.now()
            _mytimeOpts.get(this).endTime = endTime
          }

          getTime () {
            return _mytimeOpts.get(this).endTime - _mytimeOpts.get(this).startTime
          }

          test () {
            console.log('right')
          }
        }

2.  使用 symbol

        const _startTime = Symbol('startTime')
        const _endTime = Symbol('endTime')

        class mytime1 {

          constructor () {
            this[_startTime] = ''
            this[_endTime] = ''
          }

          strat () {
            this[_startTime] = Date.now()
          }

          end () {
            this[_endTime] = Date.now()
          }

          getTime () {
            return this[_endTime] - this[_startTime]
          }

        }

3.  总结

    - weakmap 模式符合所有的私有特性
    - symbol 只能保证在外部不能被发现但是调用 Reflect.ownkeys 还是可以看到这些属性
    - 最终选择 weakmap

4.  参考链接  
    [weakmap 优点](https://juejin.im/entry/572c0b2d2e958a00667a081d)  
    [weakmap 和 symbol 对比](https://www.tangshuang.net/3237.html)
