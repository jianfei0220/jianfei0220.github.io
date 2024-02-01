# vue 路由配置

## 一、路由懒加载

当打包构建应用时，JavaScript 包会变得非常大，影响页面加载。如果能把不同路由对应的组件分割成不同的代码块，然后当路由被访问的时候才加载对应组件，这样就会更加高效。

```javascript
// 静态导入：加载应用时，不论是否需要查看，都会被加载。
import UserDetails from './views/UserDetails.vue';

// 动态导入：需要时才加载，可以提高首次加载页面的速度
const UserDetails = () => import('./views/UserDetails.vue')
const router = createRouter({
  routes: [
    { path: '/users/:id', component: UserDetails }
    // 或在路由定义里直接使用它
    { path: '/users/:id', component: () => import('./views/UserDetails.vue') }
  ]
})
```

这种方法可以实现按需懒加载，并且一个组件会打包成一个 js 文件。

## 二、把组件按组分块

#### es6 中的**import （官方推荐的方式）**

有时候想把某个路由下的所有组件都打包在同个异步块 (chunk) 中。只需要使用命名 chunk，一个特殊的注释语法来提供 chunk name ：

```javascript
const UserDetails = () =>
  import("./UserDetails.vue" /* webpackChunkName: "group-user" */);
const UserDashboard = () =>
  import("./UserDashboard.vue" /* webpackChunkName: "group-user" */);
const UserProfileEdit = () =>
  import("./UserProfileEdit.vue" /* webpackChunkName: "group-user" */);
```

webpack 会将任何一个异步模块与相同的块名称组合到相同的异步块中。

#### require.ensure 方式加载- （webpack 提供的方式）

```javascript
const Register = (r) =>
  require.ensure([], () => r(require("@/views/register/index")), "register");
```

- dependencies:  依赖的模块数组
- callback:  回调函数，该函数调用时会传一个 require 参数
- chunkName: 模块名，用于构建时生成文件时命名使用
  require.ensure() 是 webpack 特有的，仅在使用 Webpack 的项目中有效。

## 三、代码拆分

**chunk-vendors.js：**包含了项目中所有来自 node_modules 的第三方库（vendors），所有第三方依赖 js 的集合。

如 Vue.js 自身、Vuex、Vue Router 或是安装的其他 npm 包。

这个文件的目的是利用浏览器的缓存能力，因为这些库代码不太可能经常更改，所以用户在访问网站时，只需要下载一次这个文件。在后续的访问中，只要这些依赖没有更新，用户可以使用缓存中的版本，加快加载速度。

**app.js：**这个文件包含了业务代码的集合。包括 Vue 组件、路由配置、状态管理等。基本上，所有不是第三方库的代码都会被打包到这个文件中。相对于 chunk-vendors.js，这个文件更频繁地更改，因为它反映了项目应用逻辑和内容的变化。

可以通过路由[懒加载](https://so.csdn.net/so/search?q=%E6%87%92%E5%8A%A0%E8%BD%BD&spm=1001.2101.3001.7020)来减少这个文件的大小（拆包），拆包后会多出很多 chunck.js 文件。

```javascript
const { defineConfig } = require("@vue/cli-service");
module.exports = defineConfig({
  transpileDependencies: true,
  productionSourceMap: false,
  chainWebpack(config) {
    config.when(process.env.NODE_ENV === "product", (config) => {
      // 分割代码
      config.optimization.splitChunks({
        chunks: "all", // 优化所有的 chunks，无论是同步还是异步加载的
        cacheGroups: {
          // 第三方组件
          libs: {
            name: "chunk-libs", //指定chunks名称
            test: /[\\/]node_modules[\\/]/, //符合组的要求就给构建venders
            priority: 10, //priority:优先级：数字越大优先级越高，因为默认值为0，所以自定义的一般是负数形式,决定cacheGroups中相同条件下每个组执行的优先顺序。
            chunks: "initial", //只打包最初依赖的第三方
          },
          // 公共组件
          commons: {
            name: "chunk-commons",
            test: path.resolve("src/components"),
            //最大初始化加载次数，一个入口文件可以并行加载的最大文件数量，默认3
            minChunks: 3,
            priority: 5,
            //这个的作用是当前的chunk如果包含了从main里面分离出来的模块，则重用这个模块，这样的问题是会影响chunk的名称。
            reuseExistingChunk: true,
            //最大初始化加载次数，一个入口文件可以并行加载的最大文件数量，默认3
            maxInitialRequests: 3,
            //表示在分离前的最小模块大小，默认为0，最小为30000
            minSize: 0,
          },
          echarts: {
            name: "chunk-echarts",
            test: /[\\/]node_modules[\\/]_?echarts(.*)/,
            priority: 40,
            chunks: "async",
            reuseExistingChunk: true,
          },
          // 将vantUI拆分为单个包
          vantUI: {
            name: "chunk-vantUI",
            // 权重需要大于libs和app，否则将被打包到libs或app中
            priority: 20,
            test: /[\/]node_modules[\/]_?vant(.*)/,
          },
        },
      });
    });
  },
});
```
