# 使用 vue 遇到的问题

- 在使用 watch 方法的时候某一个复杂对象更新后并没有触发 watch 方法
- > 原因是 vue watch 默认做的是浅层比较 需要添加 deep 参数为 true
- > 顺路说下 immediate 参数是是否第一次就去执行里面的 handler 方法
- 在打包时候出现 topostrp 包循环调用错误(实际原因还没有找到)

  ````js
  // 解决在linux中 打包报循环依赖的问题
  args[0].chunksSortMode = 'none'
  ​      return args
  ​    })```
  ````
