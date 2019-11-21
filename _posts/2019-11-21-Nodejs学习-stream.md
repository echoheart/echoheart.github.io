---
layout:     post
title:      Nodejs学习-stream
subtitle:   Nodejs学习-stream
date:       2019-11-21
author:     limantang
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - Node.js
---

# Nodejs学习-stream模块

# stream-流
stream可以认为是水流, 但是默认是没有水的
stream.write可以在水流中添加水(数据)
每次产生的小数据叫做chunk(块)
产生数据的一端叫做source(源头)
得到数据的一端叫做sink(水池)

看这段代码
```js
const fs = require('fs');
const stream = fs.createWriteStream('./big_file.txt');

for (let i = 0; i < 1000000; i++) {
  stream.write(`这是第${i}内容, 很多很多很多内容\n`);
}
stream.end();
```

这段代码可以认为  `big_file.txt`这个文件就是终端的水池
循环中每次写入的字符串就是`chunk`, 产生数据的操作可以认为是源头


## 大文件处理

看一段代码

```js
const http = require('http');
const fs = require('fs');

const server = http.createServer();
server.on('request', (request, response) => {
  fs.readFile('./big_file.txt', (err, data) => {
    if (err) return err;
    response.end(data);
    console.log('服务器数据发送完毕');
  });
});
server.listen(8888);
console.log('启动监听');
```

运行这段代码, 访问本机8888端口, 由于读取的文件很大, 在没有使用stream的情况下, 读取到的整个文件一下都加载进内存, 然后返回给客户端
这时node进程所占用的资源急剧增加, 如果读取的文件很大, 或者访问的用户很多, 由于占用资源过多很可能node程序就会挂掉

改用stream重新处理

```js
const http = require('http');
const fs = require('fs');

const server = http.createServer();
server.on('request', (request, response) => {
  const stream = fs.createReadStream('./big_file.txt');
  stream.pipe(response);
  stream.on('end', () => {
    console.log('服务器数据发送完毕');
  })
});
server.listen(8888);
console.log('启动监听');
```
这时内存使用相较之前不会占据很多
因为数据是以流的形式读取, 流的形式传递, 内存不会把整个问价都加载到内存当中, 所以资源占用很小

**上面代码中的pipe相当于一个管道, 将两个流连接起来, stream是一个流, http也是一个流, 所以可以使用pipe连接**

**管道的作用就是将两个流连接起来, 管道其实就是监听一个流,然后将数据传递给另一个流**

**其实管道可以通过事件来实现**

```js
const http = require('http');
const fs = require('fs');

const server = http.createServer();
server.on('request', (request, response) => {
  const stream = fs.createReadStream('./big_file.txt');
  stream.on('data', (chunk) => {
    console.log('传输数据中');
    response.write(chunk);
  });
  stream.on('end', (chunk) => {
    console.log('数据传输完毕');
    response.end(null);
  });
});
server.listen(8888);
console.log('启动监听');
```


## Readable Stream & Writable Stream
- Readable Stream
    - 事件: `data` `end``error``close` `readable`
    重点看一下`data`和`end`两个事件,监听`data`事件可以获取到每		次传输的数据, 监听`end`事件可以得知结束通知
    监听`readable`可以得知当前是否可读
    - 方法: `pipe``wrap``destroy``read``resume``pause`…
    这里主要了解一下`pipe`管道, `pause`暂停`resume`恢复

- Writable Stream

    - 事件: `drain``finish``error``close`
    主要了解一下`drain`这个事件, 这个单词的中文含义是**流干**
    什么意思呢, 看一段代码
    
```js
const fs = require('fs');
const writeStream = fs.createWriteStream('./big_file_for_write.txt');
let i = 100000;
function write (chunk) {

	let flag = true;
	do {
		i--;
		if (i === 0) {
			write(chunk);
		} else {
			flag = writeStream.write(chunk);
			if (flag === false) {
				console.log('写入流已经堵塞了,也就是我上游的水太多了, 写入流处理不过来了')
			}
		}

	} while (i > 0 && flag)
	if (i > 0) {
		writeStream.once('drain', () => {
			console.log('水流干了, 继续执行write');
			write(chunk);
		});
	}
}
write('hello world hello world hello world hello');
```

## Readable&Writable特点
**静止太paused**和**流动态flowing**

1. Readable

	- 默认处于静止态
	- 添加data事件监听, 就变为flowing了
	- 删除data事件监听, 就变为paused态了
	- `pause()`可以使它变为paused
	- `resume()`可以使它变为flowing
	- 添加`pipe`也是变为flowing,之前说过pipe相当于监听data事件

2. Writable

	- drain
	如果写入流被堵塞会触发`drain`, 也就是提示最好先不要再继续写了, 等处理完毕再继续写

## 创建自己的读写流
- 创建Writable

```js
const {Writable} = require('stream');

const writable = new Writable({
	write(chunk, encoding, callback) {
		console.log(chunk.toString());
		callback();
	}
});
process.stdin.pipe(writable);
```
必须调用callback, 否则不会开启下一次写入

- 创建Readable

```js
const {Readable} = require('stream');

const read = new Readable();

read.push('123456');
read.push('456789');
read.push(null);
// read.pipe(process.stdout);
read.on('data', (chunk) => {
	process.stdout.write(chunk);
});
```

这种写法的缺点是把所有的数据都push进去, 然后再去监听data事件, 也就是先全部读进来, 然后在去写

其实可以改进成边读边写

```js
const {Readable} = require('stream');

let charCode = 65;
const read = new Readable({
	read(size) {
		const char = String.fromCharCode(charCode++);
		this.push(char);
		console.log(`推入${char}`);
		if (charCode > 90) {
			this.push(null);
		}
	}
});
// read.pipe(process.stdout);
read.on('data', (chunk) => {
	console.log('编写边读');
	process.stdout.write(chunk);
});
```


## 四种流
- Writable
- Readable
- Duplex(双向流)
- Transform(转换流)

前两种前面提到过了, 说一下第三第四种

	- Duplex
	也就是可以一边读一边写的流, 这两种同时读写流是互不影响的
	- Transform
	就是一种转换流, 在中间做一些处理
	可以理解成类似 less 转 css

举个例子
实现进度条(打点)
```js
const fs = require('fs');

const {Transform} = require('stream');
const transformStream = new Transform({
	transform(chunk, encoding, callback) {
		process.stdout.write('.');
		callback(null ,chunk)
	}
});

const read = fs.createReadStream('./big_file.txt');
read.pipe(transformStream)
		.on('data', (chunk) => {
			// console.log(chunk);
		})
		.on('end', () => {
			console.log('end')
		});
```

Transform流必须对流的出口部分进行处理, 换言之就是必须保持流处于流动态flowing

**源码地址**

[GitHub - echoheart/node-stream](https://github.com/echoheart/node-stream)







