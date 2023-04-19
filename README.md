# fe-interview

14 天搞定前端面试准备

# 基础概念

## 概念：

### 1.原型 原型链

### 2.闭包

### 3.作用域 作用域链 执行上下文

- 作用域
  - 是指在程序中定义变量的区域，该位置决定了变量的生命周期。通俗地理解，作用域就是变量与函数的可访问范围，即作用域控制着变量和函数的可见性和生命周期。
- 作用域链
  - 在代码编译阶段指定具体访问变量的链路

![执行上下文.jpg](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f02e41f0bff3498990d04b7c90215a1f~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?)

### 4.eventloop 事件循环

![img](https://static001.geekbang.org/resource/image/e2/c6/e2582e980632fd2df5043f81a11461c6.png?wh=1142*834)

- 因为浏览器只有一个主线程背景下，同一时间只能处理一件任务，其他任务需要入队等待上一个任务处理完成，才能在进行下一个任务的处理

- 在进行某一个任务处理的过程中，会有很多事件被触发，比如输入事件（鼠标点击、移动、输入）等等，那么要完成这个任务，就需要把该任务关联的所有事件都完成，才算完成

- 所以 v8 引入了消息队列，来管理任务过程中发生的任务，任务按顺序入队，渲染主线程会循环从消息队列中取任务执行

- 为了更好的根据任务优先级处理任务，引入了宏任务和微任务的概念，把一些优先级高的任务

- 这个过程叫做事件循环

- 事件循环中的任务类型分为：宏任务、微任务

### 5.变量提升

### 6.箭头函数、this

### 7.回流重绘

### 8.继承

### 9.模块化（AMD \ CMD \ UMD \ Module \ ES6 export）

啥是模块化？

- 将复杂代码进行合理拆分成单独的小模块进行维护，提高开发效率

模块化和工程化的区别？

- 工程化是通过是用一些工具（如 webpack，rollup、vite 等）进行文件的编译构建打包，生成可线上部署的文件
- 模块化指的是工程代码的拆分方式，从以前的按文件拆分，到现在的几种模块化规范的产生，都是为了更好的拆分代码

有哪些模块规范？

- commonJS

  - NodeJS 提出的标准
  - 通过 module.exports 导出，require 导入
  - **同步加载**，nodejs 只有启动时加载模块，运行时不会重新加载，在浏览器端每次访问，都会出现大量请求

- ESModule

  - 通过 CORS 的方式请求外部 JS 模块

  - export default 。。。。import 。。。。

  - <script type="module" >

- AMD\CMD\Sea.js

### 10.setTimeOut、setTimeInterval、rAF

- setTimeOut
  - 声明的为宏任务
  - 如果 setTimeout 存在嵌套调用，那么系统会设置最短时间间隔为 4 毫秒
  - 每隔指定得时间间隔就将宏任务推入事件循环队列中，宏任务开始执行的时间不一定是声明的时间间隔（长任务）
    - 时间间隔只保证让任务被推入队列等待
    - 具体执行时间要看当前事件循环队列中，前面的**宏任务**加上**该宏任务的微任务队列中的微任务执行完毕**后，当前定时器宏任务才开始执行
    - 所以会出现定时器指定的任务会出现执行误差
- setTimeInterval
  - 声明的为宏任务
  - 每个事件指定时间间隔，将任务推入事件队列
  - 每个定时器实例只能在事件队列中存在一个，如果出现多个则会跳过执行
  - 可能出现多个事件连续执行
- rAF
  - 按照 60 帧对网页进行重绘，每秒钟最多重绘 60 到 70 次
  - 回调函数会在浏览器重绘之前，每次屏幕刷新时调用
- requestIdleCallback
  - 会在每次屏幕刷新时，判断当前帧是否还有多余的时间，如果有，则会调用`requestAnimationFrame`的回调函数

### 11.数组遍历有几种？

- foreach
- map
- filter
- find
- findIndex
- reduce
- some
- every
- for in
- for of
- for

### 12.前端竞态问题

https://juejin.cn/post/7127953386514677790

## 代码实现：

### 1.判断数据类型

```
typeof null //object
typeof NaN // number
```

```
obj instanceof Object
```

### 2.new 操作符

```
function myNew(Fn){
	const obj = {}
	obj.__proto__ = Fn.prototype
	Fn.call(obj)
	return typeof obj === 'object' ? {} : obj
}
```

### 3.instanceof

> 实现核心思想是 Fn.prototype === obj.**proto**

```
function myInstanceof(Fn, obj){
	const prototype = Fn.prototype
	let proto = obj.__proto__
	while(proto){
		if(prototype === proto) return true
		proto = proto.__proto__
	}
	return false
}
```

### 4.深拷贝、浅拷贝

### 5.防抖节流

### 6.promise async await（实现）

### 7.千分位分隔

### 8.字符串转化驼峰

### 9.call、apply、bind

### 10.异步任务调度器

```
const pLimt = (limit)=>{
	const quene = []
	let count = 0
	const run = (fn,resolve,...args)=>{
		count++

		fn(...args).then(res=>{
			reslove(res)
			count--
			if(quene.length>0){
				quene.shift()()
			}
		})
	}

	const enquene = (fn,resolve,...arg)=>{
		quene.push(run.bind(null,fn,resolve,...args))
		if(count < limit && quene.length>0){
			quene.shift()()
		}
	}

	const generator = (fn,...arg)=>{
		return new Proimse((resolve)=>{
			enquene(fn,resolve,...arg)
		})
	}

	return generator
}

// test case
const limit = pLimit(2);

function asyncFun(value, delay) {
    return new Promise((resolve) => {
        console.log('start ' + value);
        setTimeout(() => resolve(value), delay);
    });
}

(async function () {
    const arr = [
        limit(() => asyncFun('aaa', 2000)),
        limit(() => asyncFun('bbb', 3000)),
        limit(() => asyncFun('ccc1', 5000)),
        limit(() => asyncFun('ccc2', 4000)),
        limit(() => asyncFun('ccc3', 3000))
    ];

    const result = await Promise.all(arr);
    console.log(result);
})();
```

### 11.lazyman 链式调用

```
class LazyMan{
    task = []
    name
    constructor(opt){
        this.init(opt)
    }
    async do(){
        let len  = this.task.length
        while(len){
            await this.task.shift()()
            len--
        }
    }
    init(opt){
        this.name = opt.name
    }
    sleep(time,...args){
        const t = ()=>{
            return new Promise((reslove)=>{
                setTimeout(()=>{
                    reslove(...args)
                    console.log("lazy man: ",this.name ," is sleeping " ,...args)
                },time)
            })
        }

        this.task.push(t)
        return this
    }
    eat(...args){
        const t = ()=>{
            console.log("lazy man: ",this.name ," is eat" ,...args)
        }
        this.task.push(t)
        return this
    }
    drink(...args){
        const t = ()=>{
            console.log("lazy man: ",this.name ," is drink" ,...args)
        }
        this.task.push(t)
        return this
    }
}

// test case
const sam = new LazyMan({name:'ly'})

sam.eat('hamberger').drink('water').sleep(6000,'hhhh').drink('coffe').do()
```

### 12.数组转树

```

```

### 13.数组乱序

```
1.常见方法 arr.sort(()=>math.random() - 0.5)
sort带来的问题：
	1.sort对于长短数组使用的排序算法不同
		- 短数组 插入排序
		- 长数组 快速排序
	2.sort不同的算法导致无法确定每次统一针对两个相同元素排序

解决方案：
	使用洗牌算法Fisher–Yates
	function shuffle(arr) {
      var i = arr.length, t, j;
      while (--i) {
        j = Math.floor(Math.random() * i);
        t = arr[i];
        arr[i] = arr[j];
        arr[j] = t;
      }
    }
```

## CSS

- display 有哪些值
  - none
  - inline
  - inline-block
  - inline-table
  - table
  - table-row
  - table-cell
  - list-item
- flex 属性
  - flex-direction
  - flex-wrap
  - flex-flow
  - flex-grow / flex-shrink
  - flex:1
- position 属性
  - absolute
  - relative
  - fixed
  - sticky
- 垂直方向盒子外边距塌陷
  - 嵌套盒子
    - 通过 padding 区分
  - 兄弟盒子
    - 只设置一个盒子的 margin

#### less 和 sass 区别

#### flex 和 grid 区别

#### 组件滑动效果如何实现

#### BFC

# 工程化

## 1.webpack loader plugin 分别是啥

- loader
  - 处理其他非 js、非 css 类型的文件，进行转换
- plugin
  - 在打包过程中进行特殊需求的处理

## 2.webpack 构建流程

- 初始化
- 编译构建
- 输出文件

## 3.webpack dll

## 4.webpack tree shaking

- 什么是 treeshaking
  - 依赖于 ES6 模块特性（ES6 模块引用机制使得代码在编译阶段就确定下了模块之间的相互引用关系，故和运行时状态无关）
  - 可以对代码进行静态分析，分析出各个模块间互相引用的关系
  - 将没被引用的代码片段移除打好的包中，从而进行代码体积压缩
- treeshaking 适用场景
  - 只适用于 export 导出的变量
  - 不适用于 export 导出的对象中的属性！！！
- ## 如何配置开启 tree shaking

## 5.模块联邦

## 6.babel

- 转换高级语法为低级语法

- @babel/parser ---> 生成 ast
  @babel/transform ---> 解析源码，进行对应 node 处理
  @babel/generate ---> 生成编译后的代码，生成 sourceMap

## 7.monorepo

## 8.什么是 sourceMap

用来映射编译后的文件对应源码中具体的行列信息，方便调试

- vite 生成 sourcemap

  - build.sourcemap = true

- webpack 生成 sourcemap

  - devtool : eval | source-map | cheap | module | inline | hidden

- webpack sourceMap 有哪些配置

  cheap
  eval
  source-map

## 9.npm `peerDependencies` 啥作用

## 10.vite

# 框架

## 使用：

## 1.组件通信方式

## 2.生命周期

## 3.nextTick、watch、computed

## 4.V-model 双向绑定原理

## 5.模板编译原理

## 6.响应式原理

## 7.渲染器 render 原理

## 8.diff、双端 diff

## 9.vue3 性能提升是通过哪些方面实现的

## 10.内置组件原理（keepalive、component、translation）

### 10.1 keepalive -> LRU 算法

## 原理：

## 1.手写 mini-vue3

## 2.手写 Vuex

## 3.手写 VueRouter

# 项目难点

## 1.虚拟列表\时间分片\滚动加载\懒加载 （rAf、documentFragment）

https://blog.csdn.net/web2022050903/article/details/129050575?spm=1001.2014.3001.5502

https://zhuanlan.zhihu.com/p/436152473

https://juejin.cn/post/7159807927908302884

页面循环

https://time.geekbang.org/column/article/132931

## 2.大文件上传

## 3.前端 pdf 生成

## 4.拖拽排序

https://juejin.cn/post/7200736610371174458

## 5.列表缓存详情状态？keepalive 缓存后如何清除？

https://juejin.cn/post/7153140300817367054

# 网络

## 1.http 状态码

## 2.content-type 类型

## 3.http 缓存

- 强缓存
- 协商缓存

# 浏览器篇

## 浏览器渲染流程

### 浏览器发起 http 请求的过程

- 构建请求

  - 根据请求地址，准备构建请求行
    - 请求行信息包括

- 查找缓存

  - dns 缓存

  - 页面缓存

- 准备 ip 和端口

  - 根据 dns 进行 URL 中的域名和 ip 的映射关系查找

- 等待 tcp 队列

  - chrome 规定同一个域名一次限定只能发送 6 个 http 请求，其余请求需要进入 tcp 队列，等待其他请求完成后，在进行连接

- 建立 tcp 连接

- 发送 http 请求

### 输入 url 后发生了什么

- 浏览器进程处理用户输入的信息

  - 合法 url，则继续
  - 不合法，唤起搜索引擎进行关键字搜索

- 浏览器进程通过 ipc 进程通信，唤起网络进程，进行网络请求

  - 网络请求相关（详见如上）
    - 准备请求行
    - 检查缓存
    - dns 解析域名到具体 ip+端口
    - 进入 tcp 队列，满 6 个要继续等待
    - 发起 tcp 连接
    - 发送请求

- 网络请求完成，网络进程通知浏览器进程，响应数据开始解析，可以准备渲染进程

  - 检查当前打开的 url 是否和之前打开的页面属于同一站点（same-site）（同一站点：协议相同 + 域名相同）
    - 同一站点： 共用同一个渲染进程
    - 不同站点： 浏览器创建新的渲染进程

- 浏览器进程通知渲染进程，准备提交文档

- 网络进程进行数据解析，通过数据管道，和渲染进程进行数据传输

- 渲染进程拿到数据后，通知浏览器进程文档已提交，正在处理

- 浏览器收到确认文档提交的消息后，更新浏览器当前界面状态

- 渲染进程整合，解析页面子资源并加载，通知浏览器进程，已完成渲染

  - TODO： 渲染流程

### ‼️html、css、js 如何被浏览器进行解析成页面

- 生成 dom 树（html 转化成浏览器认识的存在内存中的 dom 结构）
- 计算样式表（继承、层叠）
- 整合 dom 树和样式表，生成新的布局树
- 计算 layertree 图层树

## 浏览器跨域

### 什么是跨域？有几种解决方式？

- 违反浏览器同源策略，从而产生跨域问题
- cors
- jsonp
- nginx

## 浏览器缓存

### 有哪几种缓存方式？区别？

#### 强缓存

- cache-control max-age=时间
- 通过比较 xxxxxx 时间和 max-age 的大小 来判断是否命中缓存

#### 协商缓存

- ETag 代表文件 hash 码，放入请求头中发起请求
- 客户端获取缓存中响应的 ETag 的值，并将其放入请求标头 If-None-Match 中，向服务端验证资源是否更新
- ETag === If-None-Match
  - 则服务端返回 304 Not Modified 直接从缓存中取值
  - 否则 200 直接从服务端返回最新资源

## 浏览器本地存储

### 有哪几种存储方式？区别？

localstorage
sessionstorage
cookie

## 浏览器事件机制

### EventLoop 是什么

### 哪些属于宏任务？哪些属于微任务？

## 浏览器垃圾回收

### 什么是垃圾回收机制？

- 原始数据存在栈空间中，引用类型存在堆空间中，当一段程序运行完，数据不在被需要，则浏览器会释放当前数据占用的内存空间，该过程叫做垃圾回收（反之如果不需要的数据一直占用内存则被称为内存泄漏）

### 垃圾回收方式

- 栈空间回收
  - JavaScript 引擎通过对执行栈内存中的每个入栈的方法执行上下文设置 ESP 指针，来实现调用完成后，自动销毁对应上下文
- 堆空间回收
  - 垃圾回收器定义：
    - 当函数的执行上下文随着方法运行时的调用销毁之后，对应上下文中保存在堆内存中的变量也需要进行销毁，但 ESP 无法销毁对应堆内存中的变量，所以此时需要 JavaScript 中的垃圾回收器
  - 垃圾回收器分类：
    - 主垃圾回收器（老生代），存放生存时间久的对象
    - 副垃圾回收器（新生代），存放生存时间短的对象
  - 垃圾回收具体流程：
    - 标记 - 清除 - 整理

### 哪些行为会造成内存泄漏

# 算法

## 数组

- 栈
  - 单调栈
    - 利用一些逻辑，让每次入栈后，栈内的元素始终保持单调递增或单调递减
