## 域名更新

原淘宝 npm 域名即将停止解析，请切换至新域名 [npmmirror.com](http://www.npmmirror.com/)

`http://npm.taobao.org` 和 `http://registry.npm.taobao.org` 将在 2022.06.30  号正式下线和停止 DNS 解析。

新域名为 `npmmirror.com`, 相关服务域名切换规则请参考：

```bash
http://npm.taobao.org => http://npmmirror.com
http://registry.npm.taobao.org => http://registry.npmmirror.com
```

```bash
npm config set registry https://registry.npmmirror.com
```

证书过期提示：

```bash
npm ERR! code CERT_HAS_EXPIRED
npm ERR! errno CERT_HAS_EXPIRED
npm ERR! request to https://registry.npm.taobao.org/vant failed, reason: certificate has expired
```

## npm 镜像源

1.设置镜像源（切换新的镜像源）：`--registry`  临时使用镜像；`registry`  持久配置镜像。

```bash
npm config set registry https://registry.npmmirror.com
```

2.还原为国外地址：

```bash
npm config set registry https://registry.npmjs.org
```

3.使用阿里定制的  `cnpm`  命令行工具代替默认的  `npm`

```bash
npm install -g cnpm --registry=https://registry.npmmirror.com
```

4.查看当前使用的镜像地址

```bash
npm config get registry
```

5.清空缓存

```bash
npm cache clean --force
```
