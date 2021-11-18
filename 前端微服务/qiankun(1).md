

# 主应用构建

- main.js 入口

```js
import { registerMicroApps, runAfterFirstMounted, setDefaultMountApp, start } from 'qiankun'
import { render, loader } from './render'
import microApps from './microApps'

// 初始化主应用，设置了默认进入子应用 setDefaultMountApp('/app1') 可以不写
render()

// 注册子应用
registerMicroApps(microApps(loader), {
    beforeLoad: [
        app => {
            console.log('%c-------------beforeLoad-------------' + app.name, 'color:#20B2AA;font-size:14px;')
        }
    ],
    beforeMount: [
        app => {
            console.log('%c-------------beforeMount-------------' + app.name, 'color:#20B2AA;font-size:14px;')
        }
    ],
    afterUnmount: [
        app => {
            c	onsole.log('%c-------------afterUnmount-------------' + app.name, 'color:#20B2AA;font-size:14px;')
        }
    ]
})

// 设置默认进入子应用
setDefaultMountApp('/app1')

// 启动应用
start()

// 第一次应用挂载
runAfterFirstMounted(() => {
  // console.log('first app mounted')
})
```

- microApps.js 微应用

```js
const microApps = loader => [
  {
    name: 'app1',
    entry: '//10.0.0.26:8082',
    container: '#subapp-viewport',
    loader,
    activeRule: '/app1'
  },
  {
    name: 'app2',
    entry: '//10.0.0.26:8083',
    container: '#subapp-viewport',
    loader,
    activeRule: '/app2'
  }
]

export default microApps
```

- render.js 渲染主应用

```js
import Vue from 'vue'
import elementUI from 'element-ui'

import router from '@/router'
import store from '@/store'
import App from './App.vue'
import { isLogin, login } from '@/utils/login'

import '@/components'
import '@/library/components'
import '@/assets/styles/index.scss'
import '@pillarjs/theme-industry/lib/index.css'
import '@/assets/icons'

Vue.config.productionTip = false

Vue.use(elementUI)

const vueRender = ({ loading }) => {
  return new Vue({
    el: '#app',
    data() {
      return {
        loading
      }
    },
    render(h) {
      return h(App, {
        props: {
          loading: this.loading
        }
      })
    },
    router,
    store
  })
}

let app = null

const render = ({ loading }) => {
  const init = () => {
    if (!app) {
      app = vueRender({ loading })
    } else {
      app.loading = loading
    }
  }

  if (isLogin()) {
    init()
  } else {
    login().then(() => {
      init()
    })
  }
}

const loader = loading => render({ loading })

export { render, loader }
```

- appMain.vue 增加一个渲染子应用的盒子

```html
<template>
  <section class="app-main">
    <router-view :key="key"></router-view>
    <div id="subapp-viewport"></div>
  </section>
</template>
```

# 微应用构建

- src/main.js 入口文件

```js
import Vue from 'vue'
import './public-path'
import { render, unmount as unmountFun } from './render'

if (!window.__POWERED_BY_QIANKUN__) {
  render()
}

export async function bootstrap() {
  console.log('%c***************microApp1 bootstrap***************', 'color:#20B2FF;font-size:14px;')
}

export async function mount(props) {
  console.log('%c***************microApp1 mount***************', 'color:#20B2FF;font-size:14px;')
  await render(props)
}

export async function unmount() {
  console.log('***************microApp1 unmount***************', 'color:#20B2FF;font-size:14px;')
  unmountFun()
}

```

- src/public-path.js  修改publicPath

```js
if (window.__POWERED_BY_QIANKUN__) {
  // eslint-disable-next-line no-undef
  __webpack_public_path__ = window.__INJECTED_PUBLIC_PATH_BY_QIANKUN__
}
```

- src/render 渲染

```js
import Vue from 'vue'
import elementUI from 'element-ui'
import VueRouter from 'vue-router'

import App from './App.vue'
import store from '@/store'
import { syncRoutesMap } from '@/router'
import { isLogin, login } from '@/utils/login'

import '@/components'
import '@/assets/styles/index.scss'
import '@pillarjs/theme-industry/lib/index.css'
import '@/assets/icons'

if (!window.__POWERED_BY_QIANKUN__) {
  // 不能同时引入多个babel-polyfill，所以做判断。import只能写在顶层，所以用require
  require('babel-polyfill')
}

Vue.config.productionTip = false

Vue.use(VueRouter)
Vue.use(elementUI)

let microApp = null
let router = null

const render = (props = {}) => {
  const { container } = props

  const init = () => {
    return store
      .dispatch('GenerateRoutes') // 获取权限放在render中，每次进入应用重新获取
      .then(routes => {
        router.addRoutes(routes) // 动态添加可访问路由表
      })
      .finally(() => {
        microApp = new Vue({
          router,
          store,
          render: h => h(App)
        }).$mount(container ? container.querySelector('#app') : '#app')
      })
  }
  // 路由实例化放在render中
  router = new VueRouter({
    base: window.__POWERED_BY_QIANKUN__ ? '/app1' : '/child/app1',
    mode: 'history',
    routes: syncRoutesMap
  })

  router.beforeEach((to, _, next) => {
    if (!to.matched.length) {
      // 如果未匹配到路由
      next('/')
    } else {
      next()
    }
  })

  if (isLogin()) {
    return init()
  } else {
    return login().then(() => init())
  }
}

const unmount = () => {
  microApp.$destroy()
  microApp.$el.innerHTML = ''
  microApp = null
  // 路由清空，否则会有多个应用的路由拦截同时存在
  router = null
}

export { render, unmount }
```

- src/router/index.js 配置

```js
import Layout from '@/views/layout/Layout'
import EmptyLayout from '@/views/layout/EmptyLayout'

const layout = window.__POWERED_BY_QIANKUN__ ? EmptyLayout : Layout

export default [
  {
    path: '/visualization',
    name: 'visualization',
    component: layout,
    meta: {
      title: '权限1',
        children: []
    },
  }
]
```

- src/view/layout/EmptyLayout.vue

```vue
<template>
  <router-view />
</template>

<script>
export default {
  name: 'emptyLayout'
}
</script>
```

- 跟目录postcss.config.js，安装postcss-plugin-namespace插件，样式隔离

```js
module.exports = () => {
  return {
    plugins: [
      require('postcss-plugin-namespace')('#app2', {
        // 不需要添加前缀的标签
        ignore: ['body', 'html']
      })
    ]
  }
}
```

```html
<!-- index.html中外套一层div，添加相应的id，微应用添加到主应用是没有body -->
<body>
  <div id="app2" style="height: 100%;width: 100%;">
    <div id="app"></div>
  </div>
</body>
```

- 跟目录vue.config.js 配置

```js
const pkgson = require('./package.json')

module.exports = {
    publicPath: process.env.NODE_ENV === 'production' ? '/child/app1/' : '/',
    configureWebpack: {
        output: {
            // 把子应用打包成 umd 库格式
            library: `${pkgson.name}-[name]`,
            libraryTarget: 'umd',
            jsonpFunction: `webpackJsonp_${pkgson.name}`
        }
    },
    devServer: {
        // 关闭主机检查，使微应用可以被 fetch，否则会提示生命周期未注册
        disableHostCheck: true,
        // 配置跨域请求头，解决开发环境的跨域问题
        headers: {
          'Access-Control-Allow-Origin': '*'
        }
    },
}
```

# 应用间通信

### props 父传子（将自身公共资源下发）

```js
import components from '@/library/components/'
import utils from '@/library/utils/'
import lodash from 'lodash'

let msg = {
  components,
  utils，
  lodash
}

registerMicroApps(
   [{
        name: 'app1',
        entry: '//10.0.0.26:8082',
        container: '#subapp-viewport',
        loader,
        activeRule: '/app1',
        props: msg
   }],
)

// 子应用中接收
export async function bootstrap({ components, utils, lodash: _ }) {
  Object.keys(components).forEach(key => {
    let component = _.cloneDeep(components[key])
    delete component['_Ctor']
    Vue.component(key, component)
  })

  Vue.prototype.$mainUtils = utils
}
```

### initGlobalState 通信

```js
import { initGlobalState } from 'qiankun'
import store from '@/store'

const { onGlobalStateChange, setGlobalState } = initGlobalState({
  subRoutes: [],
  appName: ''
})

onGlobalStateChange((value, prev) => {
  console.log('%conGlobalStateChange', 'color:red;', value, prev)
  store.commit('SET_MICRO_ROUTERS', value.subRoutes)
})
// 挂载到window
window.setGlobalState = setGlobalState

// 子应用中应用
export async function mount(props) {
  props.setGlobalState &&
    props.setGlobalState({
      appName: 'app1'
    })
  props.onGlobalStateChange &&
    props.onGlobalStateChange((value, prev) => {
      console.log('%conGlobalStateChange app1', 'color:red;', value, prev)
    }, true)

  render(props)
}

// 子传父router
SET_ROUTERS: (state, routers) => {
    state.routers = routers
    if (window.__POWERED_BY_QIANKUN__) {
        setGlobalState({
            subRoutes: routers
        })
    }
}
```

# 微前端鉴权

### 判断登录

通过cookie去取token，主应用和微应用中都可以获取，所以可以保持原逻辑不变

### 路由鉴权

还是各自应用内鉴权

# 公共资源管理

多项目共同用到的组件，工具等

```
    big-repo                            (微前端大仓库)
    |-----------app1                    (或者将每个应用作为一个仓库都可以)
    |           |------src
    |                   |------library （开发中）
    |-----------app2
    |           |------src
    |                   |------library （开发中）
    |-----------app3
    |           |------src
    |                    |-----library （开发中）
    |
```

将公共资源统一放到一个git仓库library内

### git subtree使用

1. 将library仓库添加至应用仓库的src/library位置: 在项目仓库根路径执行以下命令

```

 git subtree add --prefix=app1/src/library git@**.***.1.***:****.FE/library.git master --squash

```

执行完毕，应用仓库app1/src/路径下会出现library文件夹，--prefix=后面即是路径，你可以自由定制

2. 拉取公共仓库代码（如果公共仓库有更新）

```

 git subtree pull --prefix=src/library git@**.***.1.***:****.FE/library.git master --squash

```

3. 将app1/src/library下的内容改动推送至公共资源仓库

```

 git subtree push --prefix=src/library git@**.***.1.***:****.FE/library.git master

```

注意，你的项目代码正常推送即可，app1/src/library作为项目下的一个文件夹无需特殊处理（方便！）

4. 说到方便，每次命令都需要写公共资源仓库git@..1.:.FE/library.git地址是贼不方便！

将先将子仓库地址添加为remote：

```

 git remote add -f library git@**.***.1.***:****.FE/library.git

```

5. 此后再提交即可使用以下精简命令

```

 git subtree add --prefix=src/library library master --squash

 git subtree pull --prefix=src/library library master --squash

 git subtree push --prefix=src/library library master

```



# 部署

```
└── html/                     # 根文件夹
    |
    ├── child/                # 存放所有微应用的文件夹
    |   ├── app1/             # 存放微应用 app1 的文件夹
    |   ├── app2/             # 存放微应用 app2 的文件夹
    ├── index.html            # 主应用的index.html
    ├── css/                  # 主应用的css文件夹
    ├── js/                   # 主应用的js文件夹
```

```nginx
server {
    listen       8999;
    server_name  10.0.0.26;
    
    location / {
        root   E:/workspace/temp/demo/dist;
        index  index.html /index.htm;
        try_files $uri $uri/ /index.html last;
    }
    
    location /child/app1 {
        root   E:/workspace/temp/demo/dist;
        index  index.html index.htm;
        try_files $uri $uri/ /child/app1/index.html;
    }
    
    location /child/app2 {
        root   E:/workspace/temp/demo/dist;
        index  index.html index.htm;
        try_files $uri $uri/ /child/app2/index.html;
    }
}
```

```js
export const microApps = loader => [
  {
    name: 'app1',
    entry: '/child/app1/',
    container: '#subapp-viewport',
    loader, //loading 状态发生变化时会调用的方法。
    activeRule: '/app1',
    props: msg
  },
  {
    name: 'app2',
    entry: '/child/app2/',
    container: '#subapp-viewport',
    loader, //loading 状态发生变化时会调用的方法。
    activeRule: '/app2'
  }
]
```





# 问题

1. 主应用组件在子应用中再次注册

   报错 $attrs is readonly ，$listeners is readonly

   组件被注册后多了一个\_Ctor属性，需要深拷贝一份删除\_Ctor属性

2. 样式隔离

   - qiankun默认开启沙箱，可以确保单实例场景子应用之间的样式隔离，但是无法确保主应用跟子应用、或者多实例场景的子应用样式隔离，所有vue文件style最好都写上scoped，样式只作用于当前文件

   - strictStyleIsolation: true，开启严格的样式隔离模式。这种模式下 qiankun 会为每个微应用的容器包裹上一个 shadow dom 节点，从而确保微应用的样式不会对全局造成影响，但是会有一些问题
   - experimentalStyleIsolation :true，实验性的样式隔离特性，改写子应用所添加的样式为所有样式规则增加一个特殊的选择器规则来限定其影响范围
   - 最终的方案，postcss-plugin-namespace插件给样式添加自定义选择器，来做到样式的隔离

   ```js
   npm i postcss-plugin-namespace -D
   ```

   ```js
   // postcss.config.js
   module.exports = () => {
     return {
       plugins: [
         require('postcss-plugin-namespace')('#app2', {
           // 不需要添加前缀的标签
           ignore: ['body', 'html']
         })
       ]
     }
   }
   ```

   

3. babel-polyfill不能引入多个，子应用中需要添加判断

   ```js
   if (!window.__POWERED_BY_QIANKUN__) {
     require('babel-polyfill')
   }
   ```

4. 子应用间的跳转，使用 history api

   ```js
   window.history.pushState({}, '跳转app1', '/app1/main/auth')
   ```

5. 路由拦截中的异步请求

   路由拦截中有异步请求，应用间冲突，使跳转混乱，要注意执行顺序的问题

6. 报错Navigation cancelled from "/" to "/home" with a new navigation.路由无法跳转

   原因：路由重定向多次

   解决方法：添加一个隐藏的router-link

   ```html
   <router-link ref="redirect" :to="{ name }"></router-link>
   ```

   代码出发点击事件

   ```js
   this.$refs.redirect.$el.click()
   ```

7. 离开子应用的时候需要清空路由，否则会有多个路由拦截导致跳转混乱

   ```js
   const unmount = () => {
     microApp1.$destroy()
     microApp1.$el.innerHTML = ''
     microApp1 = null
     router = null
   }
   ```

8. 去掉子应用中body的min-height