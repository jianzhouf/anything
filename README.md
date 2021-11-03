# 常用

## react字符串替换
https://github.com/iansinnott/react-string-replace

```js
const reactStringReplace = require('react-string-replace')
reactStringReplace('Apt 111, phone number 5555555555.', /(\d+)/g, (match, i) => (
  <span key={i} style={{ color: 'red' }}>{match}</span>
));
// =>
// [
//   'Apt ',
//   <span style={{ color: 'red' }}>111</span>,
//   ', phone number ',
//   <span style={{ color: 'red' }}>5555555555</span>,
//   '.'
// ]
```

## babel macros
https://github.com/jgierer12/awesome-babel-macros

```js
//hooks.macro

import { useAutoCallback } from 'hooks.macro';
useAutoCallback(() => {
  doSomethingWith(value);
});

//Becomes:
useCallback(() => {
  doSomethingWith(value);
}, [doSomethingWith, value]);
```
## 滚动修改--var 然后实现动画效果
https://github.com/triggerjs/trigger
