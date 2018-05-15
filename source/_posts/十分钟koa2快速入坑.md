# 十分钟koa2快速入坑

![](/images/15263744179620/15263744479883.jpg)

最近因为一个项目的原因，简单了解了koa2框架。为了帮助小伙伴们刚好的入坑，特整理此文。

为啥入坑koa2？
* koa2小而美，相比express更小、更健壮、更富有表现力。
* 全面支持ES6/ES7语法；
* 结构优雅，易于扩展，360的thinkjs，阿里的egg.js全部基于koa2开发，源代码只有4个文件，500行代码；
* 站在 2018 年的节点来看，Koa2 的学习大潮已经到来；

## Node.js技术栈Web服务框架的发展
### 没有轮子的年代
早期nodejs就提供了http服务的能力，当时人们已经开始尝试使用nodejs提供web服务，类似这样：
![](/images/15263744179620/15263745240145.jpg)

没有轮子，高级一些的功能和用法全部需要自己编码实现。。

#### 英雄登场
为了降低人们的重复性劳动，更高效的使用nodejs搭建web服务，人们开始发明各种轮子。express是其中最流行的web框架，后来又出现了koa，koa2，hap。

![](/images/15263744179620/15263745571835.jpg)

再后来，基于koa，又出现了一些企业级的开发框架，例如360的thinkjs,最近，阿里也开源了它的基于koa2的框架-egg.js。这也证明了koa框架本身结构分拆的优秀，然而，无论是express 还是 koa，这些框架都来自一人之手，TJ。

TJ何许人也，前端的小伙伴们可能了解这位大神。看下面这个图：
![](/images/15263744179620/15263745743698.jpg)

他的自我介绍：TJ Holowaychuk，程序员兼艺术家，Koa、Co、Express、jade、mocha、node-canvas、commander.js等知名开源项目的创建和贡献者。
社区影响：
https://nodejsmodules.org 第一页出现次数最多的那个少年，
高产到令人发指，Quora上甚至有人猜测TJ不是一个人，而事实上他就是一个人。
substack/npmtop：对node npm社区代码贡献截止目前占到整个社区的3.04%。
好，膜拜完大神，我们继续看nodejs框架的发展。

#### 初代英雄Express诞生

![](/images/15263744179620/15263746682551.jpg)

```
var express = require('express');
var app = express();

app.get('/', function(req, res){
  res.send('hello world');
});

app.listen(3000);
```
Express 早期提供了web服务器各种常用功能的全家桶，提供了例如路由、表单解析等功能模块，中后期做了大量的拆分，将大部分模块都独立出来官方自行维护，或者是采用社区其他开发者提供的中间件模块。虽然进行了拆分，但Express总体来说是相对大而全，API 较为丰富的框架，并且它的整个中间件模型是基于 callback 回调，而 callback 随着逻辑增多，会出现callback hell的问题。

而且express对于中间件完全是顺序调用的，对于一些需要流程控制的场景，解决方案比较复杂。于是TJ大神开动异于常人的脑回路，设计了Koa。

#### 二代目-Koa登场 - 我们不一样
![](/images/15263744179620/15263747373333.jpg)

![](/images/15263744179620/15263747427072.jpg)

Koa 从一开始就选择了跟 Express 完全不同的架构方向。

首先Koa 与 Express 是在做同样事情上的不同实现，所以意味着他俩对外提供的能力大部分是相同的，我们看不同的地方：Koa 从一开始就是按照功能模块划分，各司其职。koa的 context，也就是 ctx，上面有很多引用，最核心的是 request 和 response，这俩可以对应到 Express 两个对立的 req 和 res，在 Koa 里面，把它俩都集中到 ctx 里面进行管理，分别通过 ctx.request 和 ctx.reponse 进行直接访问，原来 Express 两个独立对象做的事情，现在一个 ctx 就够了，上下文对象都在他手中，想要联系谁就能联系谁。 其次是它的中间件机制，Koa 真正的魅力所在，后面中间件环节，我们会对此进行介绍。

#### 三代目koa2 
![](/images/15263744179620/15263747633150.jpg)

![](/images/15263744179620/15263747705512.jpg)

函数式编程，ES7官方标准异步方案： async/await，你值得拥有。

目前的koa官方github已经全面的使用koa2版本的代码了，并且有一句非常重要的提示Koa requires node v7.6.0 or higher for ES2015 and async function support.。意思是说，koa需要至少node v7.6.0版本和ES2015(es6+async)才能使用。

koa2结合了async/await已经成为了最好的web开发框架。

Koa1 和 koa 框架模型，API基本相同，但是两者的中间件互不兼容，（底层实现不同），官方提供了方便的升级工具，可实现升级。

#### koa2框架定位：
* 更小、更健壮、更富有表现力；
* 免除重复繁琐的回调函数嵌套；
* 极大地提升错误处理的效率；
* 轻量、优雅，仅提供web服务最基础的函数库；
* 通过中间件扩展实现不同的高级需求；

![](/images/15263744179620/15263748325240.jpg)

koa2的源码只有四个文件，500行左右。只提供封装好http上下文、请求、响应，以及基于async/await的中间件调用机制。源码阅读此处不具体展开，有兴趣可以阅读下。
## koa2语法
### 服务实例创建
![](/images/15263744179620/15263748396979.jpg)

### 中间件
上图中，app.use(function)方法中的function就是一个最简单的中间件，通过多次app.use()方法可以在koa2框架中注册多个中间件。如下图所示：
![](/images/15263744179620/15263748493895.jpg)

与express最大的不同点，就是中间件再也不是顺序执行，而是类似洋葱和堆栈，先进后出的流程，这样可以提供更好的流程控制，例如日志处理。

著名的洋葱模型：
![](/images/15263744179620/15263748588046.jpg)

以上代码请求结果如下图所示：
![](/images/15263744179620/15263748663661.jpg)


#### 路由
koa2本身不提供路由模块，但是通过引入独立的路由模块，就可以提供类似express的路由能力，代码如下图：
![](/images/15263744179620/15263748809188.jpg)

![](/images/15263744179620/15263748845653.jpg)


以上方式实现了接口和业务逻辑的分离，可以提高业务逻辑代码的复用，职责分离。

#### 静态资源服务
koa2通过引入独立的模块，可实现类似nginx的静态资源服务能力，如果不需要反向代理等能力，甚至都不需要部署nginx了。可参考以下代码：
![](/images/15263744179620/15263748945923.jpg)



#### SSR后端渲染
为了提高首屏加载速度，有时候需要进行后端渲染，koa2通过加载其他模块也可以支持，代码参考如下：

# 安装koa模板使用中间件 npm install --save koa-views
![](/images/15263744179620/15263749116069.jpg)

# 安装ejs模板引擎 npm install --save ejs
![](/images/15263744179620/15263749193566.jpg)


#### mongodb 数据库操作


```
//npm install --save monk

//连接数据库

const monk = require('monk’); 

const url = 'localhost:27017/xgllseo’; 

const db = monk(url);

//创建一个集合并插入自增数据

db.createCollection("counters") db.counters.insert({_id:"productid",sequence_value:1})

//增

db.get('集合').insert([{ woot: 'bar' }, { woot: 'baz' }])

//删

db.get('集合').remove({"_id":1}); 

//改

db.get('集合').update({name: '旧数据'}, {name: '新数据’})

//查

db.get('集合').find({"_id":1}).then((doc)=>{})
```

#### MySQL 数据库操作 

```
# npm install mysql-pro
// init
var Client = require("mysql-pro"); 
var client = new Client({ 
	mysql: { 
		host: "127.0.0.1", 
		port: 3306, 
		database: "db", 
		user: "root", 
		password: "123456" 
	} 
});

// basic query
client.query(“select * from user where id = ?;”, [1]).then(function(result) { 
	// use result 
}, function(error){ 
	// error 
});

//transaction
async function() { 
	await client.startTransaction(); 
	await client.executeTransaction("select * from user;", []); 
	await client.executeTransaction("update user set age = ? where id = ?;", [2, 1]);
 	await client.stopTransaction(); 
}
```
以上，对koa2的一些常见语法给出了样例参考。

2018年koa2的生态系统已经比较完善，各大厂也基于koa2进行了封装，开源了一些面向工程和业务实践的框架，例如egg.js。

关于Koa2的讲解就先分析到这，希望对大家有所帮助。如有不同的看法，欢迎交流！

另附：[TJ摄影佳作欣赏](https://500px.com/tjholowaychuk)


##### 参考资料：
* https://chenshenhai.github.io/koa2-note/
* https://zhuanlan.zhihu.com/p/26216336
* https://dongliang1993.github.io/2017/12/06/koa2-%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%90-%E4%B8%80/
* https://juejin.im/post/5a6dda1d5188257330612d88

