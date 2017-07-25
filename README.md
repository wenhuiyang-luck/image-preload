# 图片预加载

浏览前预加载图片，使用jQuery封装插件。

- 案例展示：[【预览地址】]()
  - 图片无序/无序预加载，翻页展示，loading显示百分比进度。

### 初始化代码
```js
function PreLoad(imgs, options) {
  this.imgs = (typeof imgs === 'string') ? [imgs] : imgs;
  this.opts = $.extend({}, PreLoad.DEFAULTS, options);   
  if(this.opts.order === 'ordered') {
    this._ordered();    // 有序加载
  }else {
    this._unordered();  // 无序加载
  }	
}
```
### 默认参数配置
```js
PreLoad.DEFAULTS = {
	order: 'unordered',   // 无序预加载
	each: null,           // 每一张图片加载完毕后执行
	all: null             // 所有图片加载完毕后执行
};
```
### 有序加载代码
```js
PreLoad.prototype._ordered = function() {  // 有序加载
  var opts = this.opts,
    imgs = this.imgs,
    len = imgs.length,
    count = 0;  // 第几张图片

  load();

  function load() {
    var imgObj = new Image();

    $(imgObj).on('load error', function() {
      opts.each && opts.each(count);

      if(count >= len) {
        // 所有图片已经加载完毕
        opts.all && opts.all();
      } else {
        load();
      }
      count++;
    });
    imgObj.src = imgs[count];
  }
}
```
### 无需加载代码
```js
PreLoad.prototype._unordered = function() {  // 无序加载
  var imgs = this.imgs,
    opts = this.opts,
    count = 0,
    len = imgs.length;

  $.each(imgs, function (i, src) {
    if(typeof src != 'string') return;

        var imgObj = new Image();
        $(imgObj).on('load error', function () {
            opts.each && opts.each(count);

            if(count >= len-1){
                opts.all && opts.all();
            }
            count++;
        });

        imgObj.src = src;
    });
}
```

### 扩展方法
```js
$.extend({
  preload: function(imgs, opts) {
    new PreLoad(imgs, opts);
  }
});
```

### 调用
```js
$.preload(imgs, {
  order: 'unordered',       // 自定义加载顺序
  each: function(count) {
    $progress.html(Math.round((count+1)/len *100)+'%');  // 每加载一张图片改变loading百分比
  },
  all: function() {
    $('.loading').hide();   // 全部加载后隐藏loading页面
        document.title = '1/'+len;
  }
});
```
