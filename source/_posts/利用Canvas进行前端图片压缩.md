---
title: 利用Canvas进行前端图片压缩
date: 2021-02-25 10:36:00
tags:
    - JavaScript
    - Canvas
categories:
    - JavaScript
---

**纯前端利用Canvas来进行图片压缩的方法**

<!-- more -->

``` javascript
// 压缩方法
function compress(file, callback) {
  var reader = new FileReader();
  reader.readAsDataURL(file);
  reader.onload = function(e) {
    var base64Image = e.target.result;
    var image = new Image();
    image.src = base64Image;

    image.addEventListener('load', function(e) {
      var needCompress = false; // 是否需要压缩
      var ratio;
			
      // 支持最大的宽高
      var maxH = 400;
      var maxW = 800;

      var imageH = image.naturalHeight;
      var imageW = image.naturalWidth;
      
      // 宽高压缩
      if (maxH < imageH) {
        needCompress = true;
        ratio = imageH / maxH;
        maxW = imageW / ratio;
      }

      if (maxW < imageW) {
        needCompress = true;
        ratio = imageW / maxW;
        maxH = imageH / ratio;
      }

      if (!needCompress) {
        maxW = imageW;
        maxH = imageH;
      }
			
      // 创建canvas并隐藏
      var canvas = document.createElement('canvas');
      canvas.setAttribute('id', '__compress__');
      canvas.width = maxW;
      canvas.height = maxH;
      canvas.style.visibility = 'hidden';
      document.body.appendChild(canvas);

      var ctx = canvas.getContext('2d');
      ctx.clearRect(0, 0, maxW, maxH);
      ctx.drawImage(image, 0, 0, maxW, maxH);
			
      // canvas 压缩比值，不宜过小
      const compressImage = canvas.toDataURL(file.type, 0.9);
      console.log('原来大小：' + base64Image.length);
      console.log('压缩后大小：' + compressImage.length)
      console.log('压缩比：' + base64Image.length / compressImage.length);
      callback(compressImage);
    });
  }
}    
```

**使用案例**

``` html
<input id="upload" type="file" />
```

``` javascript
// 支持的类型
var ACCEPT_TYPE = [ 'image/jpeg', 'image/png', 'image/jpg' ];
const upload = document.getElementById('upload');
upload.addEventListener('change', function(e) {
	const [ file ] = e.target.files;
	if (!file) {
		return;
	}
	const { type: fileType, size: fileSize } = file;
	if (ACCEPT_TYPE.indexOf(fileType) < 0) {
		upload.value = null;
		alert('不支持的文件类型' + fileType);
		return;
	}

	compress(file, (compressImage) => console.log(compressImage));
});
```

> [案例源码](https://github.com/hackycy/practice-examples/blob/master/javascript/canvas/compress.html)