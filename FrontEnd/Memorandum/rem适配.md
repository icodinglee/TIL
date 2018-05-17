```
!(function (win, doc) {
    function setFontSize() {
        // 获取window 宽度
        // zepto实现 $(window).width()就是这么干的
        var winWidth = window.innerWidth;
        doc.documentElement.style.fontSize = (winWidth / 750) * 100 + 'px';

        // 640宽度以上进行限制 需要css进行配合
        //            var size = (winWidth / 640) * 100;
        //            doc.documentElement.style.fontSize = (size < 100 ? size : 100) + 'px' ;
    }

    var evt = 'onorientationchange' in win ? 'orientationchange' : 'resize';
    var timer = null;
    win.addEventListener(evt, function () {
        clearTimeout(timer);
        timer = setTimeout(setFontSize, 300);
    }, false);
    win.addEventListener("pageshow", function (e) {
        if (e.persisted) {
            clearTimeout(timer);

            timer = setTimeout(setFontSize, 300);
        }
    }, false);

    // 初始化
    setFontSize();

}(window, document));

```

注：750是 设计稿尺寸。

或者
```
<script type="text/javascript">
	var deviceWidth = document.documentElement.clientWidth;
	deviceWidth = deviceWidth > 600 ? 600 : deviceWidth;
	document.documentElement.style.fontSize = deviceWidth / 7.5 + 'px';
</script>
```
![截图_15209311513457(3).png](https://upload-images.jianshu.io/upload_images/5545478-f857d479f2f2f306.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![截图_15209310095666(3).png](https://upload-images.jianshu.io/upload_images/5545478-030f81aaa34c1a10.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
