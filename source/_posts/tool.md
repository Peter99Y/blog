---
title: tool
---

### 金额千分位

```
function $currency(value, options) {
      if (typeof options === 'string') {
        options = { symbol: options };
      }
      const {
        offset = 0.01,
        useGrouping = true,         // 是否启用千分位格式; 如: '222,101.00'
        symbol = '',                // 自定义金额前展示符号；如： '#0.11'
        minimumFractionDigits = 2,  // 最小小数位数长度
        maximumFractionDigits = 2,  // 最大小数位数长度
      } = Object(options);

      return Number(parseFloat(value * offset) || 0)
        .toLocaleString('arabext', {
          style: 'currency',
          currency: 'CNY',
          useGrouping,
          minimumFractionDigits,
          maximumFractionDigits,
        })
        //去除默认的¥符号;          默认返回带有¥符号；如: '¥11,111.11'
        .replace(/^(-?)¥(.*)$/, `$1${symbol}$2`);
    };

$currency('11111');      // '111.11'
$currency('asd11');      // '0.00'
```

### 禁止表单输入表情/特殊字符

```
<template>
  <input type="text" v-model="note" @change="vaidateEmoji" />
</template>

<script> export default {
    methods: {
      vaidateEmoji() {
        var reg = /[^u4E00-u9FA5|d|a-zA-Z|rns,.?!，。？！…—&$=()-+/*{}[]]|s/g
        this.note = this.note.replace(reg, '')
      },
    },
  } </script>
```

### 输入值小于最小范围取最小，大于最大范围取最大

```
Math.max(minValue, Math.min(inpValue, maxValue));
```

### 偏移多少天

```
function offsetDay(date, offsetDays){
    let defaultDate =  new Date(date) == 'Invalid Date' ?  new Date() : new Date(date);
    let pastDaysTime = defaultDate - 1000 * 60* 60 *24 * offsetDays;
    let startTime = new Date(pastDaysTime);
    var agoDay = `${startTime.getFullYear()}-${String(startTime.getMonth()+1).padStart(2, '0') }-${String(startTime.getDate()).padStart(2, '0')}`
    return agoDay;
}

console.log(offsetDay('2022-03-30', 30));      // 2022/02/28
```
