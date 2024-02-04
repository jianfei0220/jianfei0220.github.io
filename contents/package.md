# package.json 文件总结

## npm 安装命令

```bash
npm install xxx --save      // 简写：npm install xxx -S
npm install xxx --save-dev  // 简写：npm install xxx -D
// 安装的同时，将信息写入package.json中项目路径中

--save     将依赖包名称添加到 package.json 文件 dependencies 下
--save-dev 则添加到 package.json 文件 devDependencies 下
--save     是发布之后还依赖的东西
--save-dev 是开发时候依赖的东西

// 自npm@5起，已经默认将依赖项保存到 package.json 的 dependencies 中。如果使用的是npm@5或更高版本，可以省略-S参数。
```

注：
比如， babel 是发布时，将 ES6 代码编译成 ES5 ，那么 babel 就是 devDependencies。
Vue 项目中 vue-router，由于发布之后还是依赖 vue-router，所以是 dependencies。

## package.json 中^和~的区别

```bash
"dependencies": {
  "css-loader": "~2.1.0",
  "es6-promise": "^2.0.0",
}
```

#### ~符号

若 css-loader 有新的版本 2.2.0 及以上，执行 npm install 时，只会匹配到 2.1.x 的最新版本，不会匹配到 2.2.0 及以上。

#### ^符号

若 es6-promise 有新的版本 3.0.0 及以上，执行 npm install 时，只会匹配到 2.x.x 的最新版本，不会匹配到 3.0.0 及以上。

注：
npm install 时，会下载 dependencies 和 devDependencies 中的模块，
npm install –production 或者注明 NODE_ENV 变量值为 production 时，只会下载 dependencies 中的模块。
