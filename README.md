# anything

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
