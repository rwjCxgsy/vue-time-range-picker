# 基于vue开发的日期区间选择器

![示例](https://raw.githubusercontent.com/rwjCxgsy/vue-time-range-picker/master/src/GIF.gif)

[demo地址](http://47.102.114.90/demo/dist/#/range)

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

#### is-selected-close

类型：Boolean

> 选择完成后是否关闭选择器

#### holiday

类型数组

自定义第三个节日或者提醒事项 注意*日期要有准确，比如 一月一号 应该是 01-01 不是 1-1*

```javascript
 [
    {
        date: '2022-01-01',
        message: '元旦',
        color: 'red'
    }
 ]

```

### 事件

#### change

返回参数

> start 开始时间
end 结束时间
list 时间列表
