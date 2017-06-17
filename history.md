### SPA H5 history API

history API为我们提供了页面不刷新，不跳转的情况下，如何改变页面的url。

#### pushState, replaceState

```javascript
history.pushState(state, title, url);
```

* state: 状态对象，popstate事件的事件对象state会获取该参数的拷贝，可用作传递参数适用
* title: 目前大多数的浏览器忽略该参数，为安全传递空字符串为好。
* url: 改变的目标路径地址，相对路径以当前url为基准，若不是，该url首同源策略限制

pushState: 该方法会在浏览器的前进，后退的堆栈中，创建一条最新的记录，并将当前访问的页面定向到这个堆栈当中, 并且这条记录会放到历史记录中

replaceState: 替换当前浏览器url状态，并不在前进，后退的堆栈中，产生一条记录

#### 事件 popstate	

```javascript
window.addEventListener("popstate", function() {});
```

pushState, replaceState并不会触发这个事件，这个事件是由

```javascript
history.back();
history.go();
history.forward();
```

事件要准确无误的触发，要准确理解浏览器前进，后退，和准确理解pushState和replaceState的区别

#### 浏览器历史记录	 

浏览器历史记录什么时候产生？从浏览器打开一个页面开始，就会开始产生一个全新的前进，后退的行为，如果打开的页面不为空白，就会产生历史记录（对于同一链接，浏览器只会记录一次）。

#### 示例代码

```javascript


window.onpopstate = function(event) {
  console.log("location: " + document.location + ", state: " + JSON.stringify(event.state));
};

history.pushState({page: 122244434343434434}, "title 2", "?page=5");
history.replaceState({page: 122244434343434434}, "title 2", "?page=11");

history.go(-1);
setTimeout(function() {
	console.log("test");
	history.go(1);
}, 1000);

```

假设我们通过xxx.html访问上面页面，会发现输出的

```javascript
location: http://localhost:9999/weixiao_open/public/weixiao_open/index.html, state: null
test
location: http://localhost:9999/weixiao_open/public/weixiao_open/index.html?page=11, state: {"page":122244434343434430}
```

为什么第一个state是null呢？首先我们访问一个不带参数的xxx.html，然后调用pushState方法定向的我们push的历史记录，然后再替换为11，最初访问的页面并不是我们push进去的，而是初始化的xxx.html,  所以state为null, 然后我们再前进回我们push后的页面，因为我们调用了replaceState所以原先定位到的5被覆盖了，所以有以上结果

如果我们将replaceState和pushState方法调换位置，又会得到什么结果呢？答案如下，相信大家通过上面的分析已经理解了，就不过多作解释了。

```javascript
location: http://localhost:9999/weixiao_open/public/weixiao_open/index.html?page=11, state: {"page":122244434343434430}
test
location: http://localhost:9999/weixiao_open/public/weixiao_open/index.html?page=5, state: {"page":122244434343434430}
```

