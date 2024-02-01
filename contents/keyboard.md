# 安卓手机键盘弹起导致页面高度变化

## 问题描述：

**Android 表现**：当软键盘弹出时，页面（WebView）高度会发生改变，高度会变为可视区域的高度（原页面高度减去软键盘的高度）。

**IOS 表现**：当软键盘弹出时，页面（WKWebView）高度不会发生改变。

## 解决方案：

导致的原因是窗口的高度变化，可以监听窗口高度发生变化时，让 body 的高度跟初始高度保持一致。

```javascript
<script>
export default {
  mounted() {
    this.adjustBodyHeight();
  },
  methods: {
    adjustBodyHeight() {
      // 获取初始页面高度
      const originalHeight = document.documentElement.clientHeight || document.body.clientHeight;
      // 窗口大小变化时触发，键盘弹起与收起都会引起窗口的高度发生变化
      this.resizeHandler = () => {
        const resizeHeight = document.documentElement.clientHeight || document.body.clientHeight;
        if (resizeHeight < originalHeight) { // 键盘弹起
          document.body.style.height = originalHeight + 'px';
        } else {  // 软键盘收起
          document.body.style.height = '100%';
        }
      };
      // 添加 resize 事件监听
      window.addEventListener('resize', this.resizeHandler);
    }
  },
  beforeDestroy() {
    // 移除 resize 事件监听
    window.removeEventListener('resize', this.resizeHandler);
  }
}
</script>
```

## 注意：

1、不适用于使用了 **vh** 属性的页面布局，因为 vh 会根据页面的实际高度来定义自己的高度。

2、只在需要使用的页面中使用（底部固定，并且页面中存在输入框可以调起键盘的这种场景）。

## 封装：

创建一个公共方法来管理窗口大小变化的事件监听，并能在不同页面中调用。

```javascript
// resizeUtils.js
// 当前文档的高度
const getCurrentHeight = () =>
  document.documentElement.clientHeight || document.body.clientHeight;

// 获取初始页面高度
const originalHeight = getCurrentHeight();

export const adjustBodyHeight = () => {
  // 键盘弹起时，页面的高度
  const resizeHeight = getCurrentHeight();
  // 键盘弹起时，当前高度小于初始高度，则将 body 高度设置为初始高度，否则，设置为 '100%'
  document.body.style.height =
    resizeHeight < originalHeight ? `${originalHeight}px` : "100%";
};

// window 对象添加 resize 事件监听器
export const addResizeListener = (handler) => {
  window.addEventListener("resize", handler);
};

// 从 window 对象移除 resize 事件监听器
export const removeResizeListener = (handler) => {
  window.removeEventListener("resize", handler);
};
```

在 .vue 页面中使用

```javascript
<script>
import { adjustBodyHeight, addResizeListener, removeResizeListener } from '../common/untils/resizeUtils.js';
export default {
  mounted() {
    this.resizeHandler = adjustBodyHeight; // 使用公共函数作为事件处理器
    addResizeListener(this.resizeHandler); // 注册事件监听
  },
  beforeDestroy() {
    removeResizeListener(this.resizeHandler); // 注销事件监听
  }
}
</script>
```
