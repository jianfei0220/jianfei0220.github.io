# vue-cli 兼容 es2020 可选链操作符 ?.

## 一、script 支持

#### 1.安装插件：

```text
  npm i @babel/plugin-proposal-optional-chaining --save
```

#### 2.babel.config.js 配置

```javascript
module.exports = {
  ...
  plugins: [
    '@babel/plugin-proposal-optional-chaining'
  ]
};
```

## 二、template 支持

#### 1.安装插件：

```
npm i vue-template-babel-compiler --save
```

#### 2.vue.config.js 配置

```JavaScript
module.exports = {
  ...
  chainWebpack: (config) => {
    config.module
      .rule('vue')
      .use('vue-loader')
      .tap((options) => {
        options.compiler = require('vue-template-babel-compiler');
          return options;
      });
  }
}
```
