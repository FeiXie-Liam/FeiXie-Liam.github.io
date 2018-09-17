---
title: antd组件使用方式
date: 2018-09-17 20:52:02
tags:
 - 前端
 - antd
categories:
 - 前端

---

## Table

---

最基本的[Table](https://ant.design/components/table-cn/#header)组件只需要传入columns和datasource属性即可创建, 以下是最简单的一个table示例:

<!--more-->

```javascript
import { Table, Divider, Tag } from 'antd';

const columns = [{
  key:...,
  ...
}, 
  ...
];

const data = [{
  key: '1',
  ...
}, {
  key: '2',
  ...
}, 
  ...
];

ReactDOM.render(<Table columns={columns} dataSource={data} />, mountNode);
```

其中 columns属性指定表的每一列数据相关的设置, datasource 指定表中的具体数值.

**需要注意在antd中columns和datasource属性必须包含key字段, 若没有改字段, 可使用Table的rowKey属性指定列的主键**,否则会出现各类奇怪的错误.

在columns数组中, 每个数组元素表示一个column, 其具有render 属性, 可以指定该列的渲染方式. render属性接受一个方法, 可以传入三个参数(text, record, index) text表示当前渲染的元素的`dataindex`字段的值, record表示当前元素的实例对象, index表示该元素在table中的索引.