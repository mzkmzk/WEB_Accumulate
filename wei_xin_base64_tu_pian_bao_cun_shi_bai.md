# 微信base64图片保存失败

## 1. 需求

报名成功后,通过后台ajax返回数据,生成一张邀请函

因为是报名成功的人都会生成一张邀请函,所以服务器生成图片不可靠,当n人同时报名,要同时生成n张邀请函,估计是服务会GG(其实是我不会服务器弄图片)

邀请函本来是通过另外一个下载按钮来自动下载的.

但是发现iOS在浏览器随便下文件..根本不现实(这个点也折腾了好久),最后跪着求产品,产品答应长按保存图片即可(当时我是很高兴的,后来发现还有很多坑..挨个跳,挨个填)

OK,所以最后思路定位,HTML->canvas->base64->img.src->长按保存

## 2. 解决思路

html由重构师,完成,其实`HTML->canvas->base64->img.src->长按保存`很容易实现

关键代码

```javascript
  //1. 引入html5canvas库
  html2canvas($('#share_html'), {
      onrendered: function(canvas) {
      var newImg = document.createElement('img');
          newImg.src =  URL.createObjectURL(canvas.toDataURL());
          var newbody = document.body.appendChild(newImg);
      }
});
```

这样在iOS基本能完成需求,但是发现这样生成的图片清晰度太低了.

因为Retina屏是x2的,所以根据原本的HTML生成canvas清晰度很低,

解决方式很粗暴,在Retina中生成的HTML为2倍,这样生成的图片像素OK

在android中按照原来的来就OK(如果也根据2倍生成,android机很容易失去响应)

但是android微信不支持长按保存base64图片,所以但android微信长按图片弹出用其他浏览器打开

最后思路基本这样



## 3. 弯路

从上面这样一看,好像