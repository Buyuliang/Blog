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

### 标签
```bash
<view>
    <view class="c0">测试：Tom</view>
</view>

<image src="路径" style="width: 200rpx;height: 1500rpx;"></image> 
note: 为什么不使用 px 像素；rpx 自动根据分辨率缩放

<icon type="success" size="90rpx"></icon>

<navigator url="/pages/info">跳转</navigator>
note: 跳转链接

<view bindtap="clickGo">跳转</view>
clickGo(e){
  微信跳转api
  wx.navigateTo({
    url:'/pages/info',
  })
}
```

### for
```bash
js
data：{
 city:"北京"，
}，

wxml
<view>
  <text>{{city}}</text>
  <view>wx:for="{{nameList}}">{{index}}===={{item}}</view>
  <view>wx:for="{{nameList}}" wx:for-index='idx'
  wx:for-item='ele' wx:key='idx'>{{idx}}-{{ele}}</view>
</view>


    默认变量名：
        第一个<view>标签使用了默认的item和index变量名。
        {{index}}表示当前项的索引。
        {{item}}表示当前项的值。
        例如，如果nameList是["Alice", "Bob"]，则渲染结果为：

     0====Alice
     1====Bob

    自定义变量名：
        第二个<view>标签使用了自定义的idx和ele变量名。
        wx:for-index='idx'指定了索引变量名为idx。
        wx:for-item='ele'指定了当前项变量名为ele。
        wx:key='idx'用于提高渲染性能，确保每个循环项有唯一的标识符。
        例如，如果nameList是["Alice", "Bob"]，则渲染结果为：

     0-Alice
     1-Bob

证据支持

    默认变量名：
        根据当不指定wx:for-item和wx:for-index时，默认使用item和index作为变量名。
        例如：

    <view wx:for="{{array}}">
      {{index}}: {{item.message}}
    </view>

    自定义变量名：
        根据可以通过wx:for-item和wx:for-index属性来指定自定义的变量名。
        例如：

    <view wx:for="{{array}}" wx:for-index="idx" wx:for-item="itemName">
      {{idx}}: {{itemName.message}}
    </view>

    wx:key的作用：
        根据wx:key用于提高列表渲染性能，确保每个循环项有唯一的标识符。
        例如：

    <view wx:for="{{array}}" wx:for-index="idx" wx:for-item="itemName" wx:key="idx">
      {{idx}}: {{itemName.message}}
    </view>

```

### if
```bash
<view>
 <text wx:if="{{city=='北京1'}}">开</text>
 <text wx:elif="{{city=='北京2'}}">开</text>
 <text wx:else>开</text>
</view>
```

### 标签：`block` 和 `hidden`

#### `block` 标签
**用途**：
一个不可见的包裹性标签，用于组织代码结构或配合逻辑控制（如 `wx:if`、`wx:for`），**不会渲染到页面**。

**语法**：
```xml
<block wx:if="{{condition}}">
  <view>内容1</view>
  <view>内容2</view>
</block>

<block wx:for="{{list}}" wx:key="id">
  <view>{{item.name}}</view>
</block>

特点：

    无实际 DOM 节点，仅作为逻辑容器
    支持所有控制属性（wx:if/wx:for/wx:key）
    可嵌套使用

使用场景：

    批量控制一组组件的显示/隐藏
    优化列表渲染结构
    避免不必要的额外节点

hidden 属性

用途：
控制组件显示/隐藏（类似 display: none），组件仍会被渲染到 DOM 中。

语法：
```bash
<view hidden="{{isHidden}}">内容</view>
```

特点：

    通过布尔值控制显隐
    组件始终存在 DOM 中，只是不可见
    性能消耗低于频繁切换 wx:if

与 wx:if 的区别：
特性	hidden	wx:if
DOM 是否存在	是（仅隐藏）	否（动态移除）
适用场景	频繁切换显隐	初始条件决定显隐
性能	切换快，初始消耗大	切换慢，初始优化

```bash
组合使用示例

<block wx:if="{{showBlock}}">
  <view hidden="{{isHidden}}">动态内容</view>
</block>
```

最佳实践建议
    高频切换：用 hidden（如 Tab 切换）
    初始条件控制：用 wx:if（如权限校验）
    复杂结构：用 <block> 包裹保持代码整洁
    ⚠️ 注意：hidden 对自定义组件可能失效，需用 wx:if 替代。

### 数据绑定
```bash
<text>{{name}}</text>
<button bindtap="changeName">修改</button>
<input type="text" value="{{name}}" bindinput="dochange" plceholder="请输入"/>
<input type="text" model:value="{{name}}" bindinput="dochange2" plceholder="请输入"/>
```
