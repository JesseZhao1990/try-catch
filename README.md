# 总结js中的try catch

try...catch 可以测试代码中的错误。try 部分包含需要运行的代码，而 catch 部分包含错误发生时运行的代码。

语法：

```
try
{
	//要运行的代码
}
catch(err)
{
	//出现错误时要允许的代码
}
```

**大多数情况下，try catch适用于三种场合：**

1、浏览器原罪的场合：也就是兼容性场合，由于不同浏览器的报错提示是不一样的，根据捕获的浏览器的报错提示判断用户的浏览器，然后做出对应的措施。

2、中间有繁琐的判断的。比如说筛选面板。初始化的时候有太多的下拉框选项需要从后台加载，可能我没必要每一个下拉框都进行错误处理，因为我只关注所有的查询选项都加载成功，我才能发起查询的请求。只要有一个下拉框选项没加载成功，我就把错误抛给用户，不再发起查询的请求。再比如window.a.b.c.d 我要判断d等于不等于5。那如果window.a根本不存在，我根本没法判断d。所以，我要用n个if判断a b c 分别存在。再判断d。这还不如直接一个try catch呢。只要中间出错。我就直接执行catch。

使用try catch

```
try
{
	window.a.b.c.d !== 5;
}
catch(err)
{
	console.log("hello world!")
}

```

不使用try catch

```
if(window.a){
	if(window.a.b){
		if(window.a.b.c){
			if(window.a.b.c.d!==5){
				console.log("hellow world!")
			}
		}
	}
}

```
有没有崩溃的感觉。即使用&&来简化一下，也需要写成这样的

```
if(window.a && window.a.b && window.a.b.c && window.a.b.c.d !==5){
	console.log("hello world!");
}

```

3、用在nodejs中异常的处理。比如写爬虫。如果半夜三更，你的爬虫遇到了复杂网页的未知错误。如果没用try catch。你的线程就崩溃。程序就停止了。但是如果有了try catch。遇到错误就放弃处理这个网页。转向爬去其他页面。

**try catch 中的坑**

try catch只能捕获当次事件循环内的异常，对call back执行时抛出的异常将无能为力

```
function jsonp(setting){
  setting.data = setting.data || {}
  setting.key = setting.key||'callback'
  setting.callback = setting.callback||function(){} 
  setting.data[setting.key] = '__onGetData__'

  window.__onGetData__ = function(data){
    setting.callback (data);
  }

  var script = document.createElement('script')
  var query = []
  for(var key in setting.data){
    query.push( key + '='+ encodeURIComponent(setting.data[key]) )
  }
  script.src = setting.url + '?' + query.join('&')
  document.head.appendChild(script)
  document.head.removeChild(script)

}

try
{
  jsonp({
  url: 'http://photo.sina.cn/aj/index',
  key: 'jsoncallback',
  data: {
    page: 1,
    cate: 'recommend'
  },
  callback: function(ret){
    console.hhhhhhh(ret);   // 这是一句错误的语句。但是并没有被catch到。
  }
  })
  console.log("jsonp已经执行");
}catch(err)
{
  console.log(err);
}

```

上边这个例子中，首先定义了一个函数jsonp。这个函数有回调函数。在我运行jsonp的时候.我向jsonp中传送的回调函数有故意写错的地方。但是在控制台里并没有把这个错误打印出来。说明回调函数运行时的错误try catch无法捕捉到。[演示地址](http://codepen.io/zhaojianxin/pen/xEzBwk?editors=0011)
















