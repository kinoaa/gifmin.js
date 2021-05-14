# gifmin.js
压缩GIF图片
# API
gifmin(array, colors)
## 参数说明
### `array` 
是一个由ArrayBuffer, ArrayBufferView, Blob, DOMString 等对象构成的 Array ，或者其他类似对象的混合体，它将会被放进 Blob。DOMStrings会被编码为UTF-8。
### `colors`
取值0~256之间，代表gif图的色盘，值越小压缩后的文件越小色彩越单调，值越大压缩后的文件越大色彩越丰富
# 实例
## Vue使用Demo
**由于压缩需要一定时间，建议加个loading效果**
1. 下载`gifmin.min.js`到本地，放入静态资源文件夹
2. 在`index.html`引入
```js
<script src="/lib/gifmin.js"></script>
```
3. 使用Demo
```js
gifImg(file) {
      const loadings = this.$loading({
        lock: true,
        text: 'GIF图片压缩中...',
        spinner: 'el-icon-loading',
        background: 'rgba(0, 0, 0,0.7)'
      })
      const that = this
      if (window.FileReader) {
        var fr = new FileReader()
        fr.readAsArrayBuffer(file)
        fr.onload = function(e) {
          var colors = 70 // 介于0~256之间数值越小压缩后文件越小
          // eslint-disable-next-line no-undef
          var result = gifmin(fr.result, colors) // 二进制文件流
          console.log(result)
          var obj = new Blob([result], { // 转换成Blob对象
            type: 'application/octet-stream'
          })
          that.gifFile = new window.File([obj], file.name, { // 转换成file文件
            type: file.type
          })
          that.gifUrl = window.URL.createObjectURL(file)
          that.formData.gifUrl = 'yes'
          loadings.close()
        }
      }
    }
```
## 压缩GIF后上传，实现GIF压缩上传（jq使用Demo）
`记得先下载min.js到本地，然后引入哦！<script src="../js/lib/gifmin.min.js"></script>`
```JavaScript
/**
 * 压缩方法
 * @param {Object} file 需要压缩的GIF文件
 * @param {Number} colors 0~256
 * @param {String} classId  回显div的Id
 * @param {String} uploadId  选择文件input的Id
 */
function compressGif (file,colors,classId, uploadId) {
    var _file = file;
    console.log(_file)
    if (window.FileReader) {
      var fr = new FileReader();
      fr.readAsArrayBuffer(_file);
      fr.onload = function (e) {
        console.log(fr.result)//fr.result===e.target.result为ArrayBuffer文件
        var result = gifmin(fr.result, colors);
        console.log(result)
        var obj = new Blob([result], {//转化Blob对象
          type: 'application/octet-stream'
        });
        console.log(obj)
        const Blob2ImageFileForWXBrowser = new window.File([obj], _file.name, {//将压缩好的GIF转化成file文件
          type: _file.type
        });
        console.log(Blob2ImageFileForWXBrowser)
        var gifUrl = window.URL.createObjectURL(file); //GIF图片回显用
        // 回显图片
        $('.' + classId).siblings().remove();
        if (classId == 'upload-btn') {
          $('.' + classId).before(`<div class="upload-item upload-item-one" style="display:inline-block;margin-right:5px;">
                  <img src="${gifUrl}" index-video='1'>
              </div>`);
        }
        uploadImgs.push(Blob2ImageFileForWXBrowser);//添加到上传参数中
        $('#' + uploadId).val('');
        $('.upload-item-one').onload = function () {
          // 图片加载完成后销毁url，节省性能
          window.URL.revokeObjectURL(gifUrl);
        }
      }
    }
}
```
## 压缩GIF后下载，实现在线压缩
```JavaScript
/**
 * 下载方法
 * @param {String} blobUrl 需要压缩的GIF文件Blob对象转化的url
 * @param {String} filename 下载文件的文件名
 */
function downloadFileByBlob(blobUrl, filename) {
  const eleLink = document.createElement('a')
  eleLink.download = filename
  eleLink.style.display = 'none'
  eleLink.href = blobUrl
  // 触发点击
  document.body.appendChild(eleLink)
  eleLink.click()
  // 然后移除
  document.body.removeChild(eleLink);
  // 图片下载后销毁url，节省性能
  window.URL.revokeObjectURL(blobUrl);
}
/**
 * 压缩方法
 * @param {Object} file 需要压缩的GIF文件
 * @param {Number} colors 0~256
 */
function compressGif (file,colors) {
    var _file = file;
    console.log(_file)
    if (window.FileReader) {
      var fr = new FileReader();
      fr.readAsArrayBuffer(_file);
      fr.onload = function (e) {
        console.log(fr.result)//fr.result===e.target.result为ArrayBuffer文件
        var result = gifmin(fr.result, colors);
        console.log(result)
        var obj = new Blob([result], {//转化Blob对象
          type: 'application/octet-stream'
        });
        var blobUrl = window.URL.createObjectURL(obj);
        //调用下载方法
        downloadFileByBlob(blobUrl,'1.gif')
      }
    }
}
```




