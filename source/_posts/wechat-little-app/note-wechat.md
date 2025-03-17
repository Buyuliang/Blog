---
title: wechat
date: 2025年3月18日 0:09:45
tags: [wechat]
categories: wechat
---


### 文档目录 
``` bash
pages       页面文件夹
xxx.js      
xxx.wxss    等同于 CSS
xxx.wxml    等同于 HTML
xxx.json    用于对页面配置
```

### 页面标题文本
```bash
1 直接修改 page.json 文件
{
  "navigationBarTitleText": "豆瓣 - 电影"
}

2 使用 JS 修改
wx.setNavigationBarTitle({
      title: that.data.mername//页面标题为路由参数
})

```

### 设置默认启动界面
```bash
app.json 文件

  "pages": [
    "pages/index/index"
  ]
```

### 调用自定义接口
```bash
# 自定义函数
const formatTime = date => (
    ...
)

# 导出 formatTime 接口
module.exports = (
    formatTime
)

# 其他文件调用接口
const util = require('xxx.js')

...
util.formatTime(new Date(log))
...

```

### 允许爬虫爬取页面内容
```bash
sitemap.json

{
    "desc": "关于本文件的更多信息，请参考文档 https://developers.weixin.qq.com/miniprogram/dev/framework/sitemap.html",
    "rules": [{
    "action": "allow",
    "page": "*"
    }]
}
```