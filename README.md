# mobile-touch-event
移动端touch事件封装与click延迟问题解决方案
## 移动端touch事件封装与解决方案



## 1.认识移动端touch事件

> ```touch 事件是移动端的触摸事件 而且是一组事件
> touch 事件是移动端的触摸事件 而且是一组事件
> ```

- touchstart   当手指触摸屏幕的时候触发
- touchmove    当手指在屏幕来回的滑动时候触发
- touchend     当手指离开屏幕的时候触发
- touchcancel  当被迫终止滑动的时候触发（来电，弹消息）

> ```
> 利用touch相关事件实现移动端常见滑动效果和移动端常见的手势事件
> ```

### 1.1 touch事件的使用

```
使用touch:
1.绑定事件：box.addEventListener('touchstart',function () { });
2.事件对象：
名字：TouchList------触摸点（一个手指触摸就是一个触发点，和屏幕的接触点的个数）的集合
changedTouches    改变后的触摸点集合
targetTouches     当前元素的触发点集合
touches           页面上所有触发点集合
3.触摸点集合在每个事件触发的时候会不会去记录触摸
changedTouches 每个事件都会记录
targetTouches，touches 在离开屏幕的时候无法记录触摸点
4.分析滑动实现的原理：
4.1 就是让触摸的元素随着手指的滑动做位置的改变
4.2 位置的改变：需要当前手指的坐标
4.3 在每一个触摸点中会记录当前触摸点的坐标 e.touches[0] 第一个触摸点
4.4 clientX clientY      基于浏览器窗口（视口）
4.4 pageX   pageY        基于页面（视口
```

## 2. 利用touch事件封装滑动事件（swipe)

```
window.onload = function () {
    /*1. 理解移动端的手势事件*/
    /*2. swipe swipeLeft  swipeRight swipeUp swipeDown */
    /*3. 左滑和右滑手势怎么实现*/
    var bindSwipeEvent = function (dom,leftCallback,rightCallback) {
        /*手势的条件*/
        /*1.必须滑动过*/
        /*2.滑动的距离50px*/
        var isMove = false;
        var startX = 0;
        var distanceX = 0;
        dom.addEventListener('touchstart',function (e) {
            startX = e.touches[0].clientX;
        });
        dom.addEventListener('touchmove',function (e) {
            isMove = true;
            var moveX = e.touches[0].clientX;
            distanceX = moveX - startX;
        });
        dom.addEventListener('touchend',function (e) {
            /*滑动结束*/
            if(isMove && Math.abs(distanceX) > 50){
                if(distanceX > 0){
                    rightCallback && rightCallback.call(this,e);
                }else{
                    leftCallback && leftCallback.call(this,e);
                }
            }
            /*重置参数*/
            isMove = false;
            startX = 0;
            distanceX = 0;
        });
    }
    bindSwipeEvent(document.querySelector('.box'),function (e) {
        console.log(this);
        console.log(e);
        console.log('左滑手势');
    },function (e) {
        console.log(this);
        console.log(e);
        console.log('右滑手势');
    });

}
```

## 3. 利用touch事件封装移动端click事件

> click事件会在移动端出现300ms的延迟

### 3.1  利用移动端touch事件封装click事件

```
window.onload = function () {
    /*使用tap事件*/
    /*1. 响应的速度比click要快   150ms */
    /*2. 不能滑动*/
    var bindTapEvent = function (dom, callback) {
        /*事件的执行顺序*/
        /*在谷歌浏览器模拟看不到300ms的效果*/
        /*在真机上面才能看看到延时效果*/
        var startTime = 0;
        var isMove = false;
        dom.addEventListener('touchstart', function () {
            //console.log('touchstart');
            startTime = Date.now();
            /*Date.now();*/
        });
        dom.addEventListener('touchmove', function () {
            //console.log('touchmove');
            isMove = true;
        });
        dom.addEventListener('touchend', function (e) {
            //console.log('touchend');
            console.log((Date.now() - startTime));
            if ((Date.now() - startTime) < 150 && !isMove) {
                callback && callback.call(this, e);
            }

            startTime = 0;
            isMove = false;
        });
        /*dom.addEventListener('click',function () {
         //console.log('click');
         });*/
    }

    bindTapEvent(document.querySelector('.box'), function (e) {
        console.log(this);
        console.log(e);
        console.log('tap事件')
    });
}
```

### 3.2 利用插件解决移动端click延迟问题

> ```
> 使用一个叫：fastclick.js 提高移动端click响应速度
> ```

> fastclick.js[下载地址](https://cdn.bootcss.com/fastclick/1.0.6/fastclick.min.js) https://cdn.bootcss.com/fastclick/1.0.6/fastclick.min.js

- 使用方法

   1.引入插件文件

> ```
> <script src="../js/fastclick.min.js"></script> 
> ```

1.  初始化

> ```
> <script>
>     /*当页面的dom元素加载完成*/
>     document.addEventListener('DOMContentLoaded', function() {
>         /*初始化方法*/
>         FastClick.attach(document.body);
>     }, false);
>     /*就可以正常使用click事件了*/
> </script>
> ```
