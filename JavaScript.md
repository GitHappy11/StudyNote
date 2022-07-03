# JavaScript

## 好用的库收集

**day.js** 

轻量的处理时间和日期的库

```html
<script src="https://cdn.bootcdn.net/ajax/libs/dayjs/1.11.0/dayjs.min.js"></script>
```
```javascript
dayjs()
  .startOf('month')
  .add(1, 'day')
  .set('year', 2018)
  .format('YYYY-MM-DD HH:mm:ss')
```
```javascript
//无参数就默认获取当前时间戳 Data.now 
 return dayjs().format('YYYY-MM-DD HH:mm:ss')
```

**GitHub及文档**：[iamkun/dayjs: ⏰ Day.js 2kB immutable date-time library alternative to Moment.js with the same modern API (github.com)](https://github.com/iamkun/dayjs)

**CDN：**[dayjs (v1.11.0) - Day.js 是一个轻量的处理时间和日期的 JavaScript 库，和 Moment.js 的 API 设计保持完全一样. 如果您曾经用过 Moment.js, 那么您已经知道如何使用 Day.js | BootCDN - Bootstrap 中文网开源项目免费 CDN 加速服务](https://www.bootcdn.cn/dayjs/)