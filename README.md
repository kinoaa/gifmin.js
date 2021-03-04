# gifmin.js
压缩GIF图片
# API
gifmin(array, colors)
## 参数说明
### `array` 
是一个由ArrayBuffer, ArrayBufferView, Blob, DOMString 等对象构成的 Array ，或者其他类似对象的混合体，它将会被放进 Blob。DOMStrings会被编码为UTF-8。
### `colors`
- 取值0~256之间，代表gif图的色盘，值越小压缩后的文件越小色彩越单调，值越大压缩后的文件越大色彩越丰富
