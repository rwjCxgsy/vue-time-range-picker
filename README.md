# 基于vue开发的日期区间选择器

![示例](./src/Gif.gif)

[demo地址]()

## 安装

`npm i vue-time-range-picker -d`

或者使用yarn

`yarn add vue-time-range-picker`

## 使用

```javascript


import Range from 'vue-time-range-picker'

...
components: {Range},
...
```

### 参数

#### date-range

类型：Array

> 给定一个时间区间：比如['2019-08-01', '2019-10-01']

#### v-model

类型：Boolean

> 是否显示日期选择器

#### max-day

类型：Number

> 最大显示天数

#### start-text

类型：String

> 起始时间描述

#### end-text

类型：String

> 结束时间描述