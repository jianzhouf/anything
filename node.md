## Node path
## require.resolve

```
// node_modules/.bin/NODE_MODULE_NAME 可执行文件 软链接到模块中
const bin = require.resolve('.bin/NODE_MODULE_NAME');
```
