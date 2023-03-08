---
layout: old
title: ByteDance
date: 2022-03-23 15:35:29
tags: 加密

---

## 从输入url到页面展示的过程
> 纯文字题如下解答
> 知识点： TCP/IP协议族分为四层：应用层 TCP传输层 IP网络层 链路层
> 消息在发送前将会被分解为许多片段 （数据包） 通过应用层进入TCP层之后 每个数据包将会被分配一个端口号 端口号用来确定目标计算机的哪一个应用程序要使用该数据包
> TCP是一个面向连接可靠的协议 TCP三次握手 每个收到的数据包都会向发送方发送ACK确认 偏题了

1. 解析url拿到域名地址
2. dns解析 查询浏览器缓存 如果没有 则 查询本地缓存 如果没有则 查询路由缓存 路由器上面 如果没有则往上递归查询dns服务器 
3. 向ip地址发送http服务请求 浏览器与目标服务器建立tcp连接 三次握手

   1. 第一次握手向服务器发送请求等待服务器确认 SYN

   2. 第二次服务器收到请求并回复 确认指令 SYN/ACK

   3. 客户端收到服务器指令后返回确认 ACK

4. 请求和访问数据 通过get请求到服务器上的目标文件返回到浏览器进行渲染

5. 浏览器渲染页面 客户端拿到服务器发送过来的文件后 浏览器找到html文件和MIME文件 通过MIME文件知道需要用渲染引擎渲染html文件

   1. 渲染引擎将接收到的html文件 首先经过tokeniser标记化 通过词法分析将输入的html内容解析成多个标记 根据识别后的标记进行dom树构造 在dom树构造过程中会创建document对象 以document为根节点的dom树进行构造

   2. 如果遇到了script 标签则会停止html解析转而去解析script中的js 因为浏览器并不知道script标签内的js是否会改变当前html结构

   3. 解析完成后获得dom tree 然后主线程开始解析CSS并确定每个节点的样式 如果没有自定义的css样式浏览器则会加载默认的css样式

   4. 获得带样式的dom tree 后还需要知道他们在应该放在什么位置 这一过程为layout布局 主线程通过遍历dom树结构和计算好的样式来生成layout tree  layout tree上面每个节点都记录了xy坐标

   5. 为了保证正确的绘制顺序主线程会遍历layout tree创建`绘制记录表` 该表记录了绘制的顺序 这个阶段被称为绘制 

   6. 主线程遍历layout tree 生成 layer tree 当layer tree 生成完毕和绘制顺序确认后 主线程将这些信息传递给合成器线程 合成器线程将每个图层栅格化 由于一层可能像页面那么大 因此合成器线程将他们切割成许多图块 然后将每个图块发送给栅格线程 栅格线程栅格化每个图块 并见他们存储到GPU内存中 当图层栅格化完成后合成器线程将收集称为draw quads的图块信息 这些信息记录了图块在内存中的位置和在页面中的哪个位置绘制图块信息 根据这些信息 合成器生成了一个合成器帧 然后合成器帧通过IPC传送给浏览器进程 接着浏览器进程将合成器帧传送到GPU然后由GPU渲染展示到屏幕上

   7. 当滚动了页面则会生成一个新的合成器帧 新的帧再传给GPU 然后再次渲染到屏幕上 

   8. 当我们改变一个元素的尺寸位置属性时会重新进行样式计算 布局绘制以及后面的所有流程 这种行为我们称为重排

   9.  当我们改变一个元素的颜色时不会触发布局改变但是还是会触发样式计算和绘制 这个就是重绘 



## 执行顺序

```javascript
console.log('begin');

setTimeout(() => {
  console.log('setTimeout 1');
}, 0);

new Promise(resolve => {
  console.log('promise 1');
  resolve()
}).then(() => {
  console.log('promise 2');
});

(async function foo (params) {
  console.log('async 1');
  await asyncFunction()
  console.log('async 2');
})();

console.log('end');
```
#### 解析：
     1. 从上往下 console.log('begin')打印是毋庸置疑的 
     2. 遇到setTimeout 放入宏观队列
     3. 继续往下 promise 直接执行里面的内容 打印 promise 1
     4. 遇到.then 放入微观队列 
     5. 继续往下 遇到立即执行函数 打印async 1 
     6. 遇到await 将他之后的代码放入微观队列
     7. 往下同步执行 打印end
     8. 寻找微观队列中的任务 按顺序找到promise 2
     9. 继续往下找到 asyncFunction 报错 因为未定义


## 实现一个promisify 
```javascript

function promisify(func) {
  return (...arg) => new Promise((resolve, reject) => {
    func(...arg, (err, arg) => {
      if (err) reject(err)
      else resolve(arg)
    })
  })
}
const readFilePromise = promisify(fs.readFile)

readFilePromise('./conf.js').then(data => {
  console.log(data)
}).catch(err => {})

```

## Dom中增加节点 删除节点 复制节点 查询节点的方法

1. 增加节点 document.createElement('xxx')
2. 删除节点 parentNode.removeChild('xxx')
3. 复制节点 Node.cloneNode('xxx')
4. 查询节点 document.getElementById('')


## 数组去重
``` javascript 
  /**
   * array = [{
   *    key:value
   * }]
   * 
   */
  const q = function (array) {
  const map = new Map(), temp = [];
  for (let i = 0; i < array.length; i++) {
    const val = array[i].key
    if (map.has(val)) {
      array.splice(map.get(val), 1)
    } else {
      map.set(val, i)
    }
  }
  return array
}
```
原理很简单： 在哈希表中找是否存在该key 如果存在则去除当前索引如果不存在则添加到哈希表


## 盒模型
1. 如何设置盒模型 
   1. box-sizing: content-box  指定盒子模型为W3C（标准盒模型）
   2. box-sizing: border-box   为IE盒子模型（怪异盒模型）
2. 他们的区别是什么
   1. 标准盒模型又称W3C标准盒模型，其中标准盒模型的 width 等于 content 的宽度，标准盒模型的 height 等于 content 的高度 标准盒大小计算公式：width(content) + padding + border + margin

   1. 怪异盒模型又称IE盒子模型，其中怪异盒子模型的 width 等于 content + padding + border 的宽度，怪异盒子模型的 height 等于 content + padding + border 的高度 怪异盒大小的计算公式：width(content + padding + border) + margin
   
