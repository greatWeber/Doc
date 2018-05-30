# mui - 打开页面

## window.location.href

这个方法在app中可以使用，但不推荐，特别是带有带有参数的页面跳转时，页面可能会出错

## mui.openWindow
推荐使用的方法，mui内部做了一些其他的处理，问题是偶尔有bug，会出现页面打不开或者跳到别的页面(无法理解)

```
/**
 * id: 页面的标识，尽量都加上，后期可以通过plus.webview.getWebviewById来获取页面（但在实际应用中，mui好像没有保存通过openWindow打开的页面）
 * url: 页面地址
 * extras: 页面传递的参数
 * @type {String}
 */
mui.openWindow({
    id: 'index',
    url: 'index.html',
    extras: {

    }
    })

```


## plus.webview.create
创建一个页面，但不显示，需要通过show方法显示

```
/**
 * id: 页面的标识
 * url: 页面地址
 * style: 打开页面的样式
 * extras: 页面传递的参数
 */
var ws = plus.webview.create(url,id,style,extras);
ws.show();
```


## 封装
看代码，理解一下我的思路

```
/**
 * 重写openWindow
 * @param  {[type]} params [description]
 * @return {[type]}        [description]
 * 参数说明
 * id
 * url
 * styles
 * extras
 * close: 是否关闭当前页面
 */
function openWindow(params){
   var data = {
        id: params.id || '',
        url: params.url || '',
        styles: params.styles || '',
        extras: params.extras || '',
        close: params.close || false
    };
    var ws = plus.webview.getWebviewById(data.id);
    plus.nativeUI.showWaiting('页面正在拼命加载中');

    if(ws == null){
        ws = plus.webview.create(data.url,data.id,data.styles,data.extras); 
    }else{
        ws.reload();
    }
    
    setTimeout(function(){
        ws.show('slide-in-right',500,function(){ 
            plus.nativeUI.closeWaiting();
        },data.extras);
    },200)

    if(data.close){
        var current = plus.webview.currentWebview();
        setTimeout(function(){
             current.close();
        },1000)
    }
}
```

其实我觉得mui打开一个页面代价太大了，特别当页面比较大的时候，会有明显的白屏，所以我想通过create把页面存下来，通过show来打开。但是现实是残酷的，mui并没有把页面存下来，每次都要重新创建一个。-。-(不能理解)

## 总结

mui真难懂，特别是在页面逻辑方面不理想
