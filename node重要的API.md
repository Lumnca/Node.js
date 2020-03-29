## :maple_leaf:node重要的API ##

:arrow_down:[命令行工具CLI以及FS API](#a1)

***
<p id="a1"></p>

**:fallen_leaf:命令行工具CLI以及FS API**

本章将介绍使用Node.js中一些重量级APl：处理进程（stdio）的stdin以及stdout相关的API，还有那些与文件系统（fs）相关的API。
第4章中我们介绍过，Node通过使用回调和事件机制来实现并发。这些API会让你首次接触到基于非阻塞事件的I/O编程中的流控制。
除了介绍如何使用这些API之外，你还可以通过本章来构建首个应用：一个简单的命令行文件浏览器，其功能是允许用户读取和创建文件。

我们从定义需求开始：

* 程序需要在命令行运行。这就意味着程序要么通过node命令来执行，要么直接执行，然后通过终端提供交互给用户进行输入、输出。
* 程序启动后，需要显示当前目录下列表。
* 选择某个文件时，程序需要显示该文件内容。

**编写首个Node程序**

* 1.创建模块

和其他例子一样，我们从创建一个项目目录开始。按照此项目的需求，我们将该目录命名为file-explorer。

正如其他章节所述，在项目中定义package.json文件始终是最佳实践。这样，既可以方便地对NPM中注册的模块依赖进行管理，将来也能对模块进行发布。
尽管此项目仅仅用到Node.js的核心模块API（因此，不会从NPM仓库中获取模块），但是，我们还是需要创建一个简单的package.json文件：

```js
{
    "name" : "file-explorer",
    "version": "1.0.0",
    "description": "this is file explorer"
}
```

要验证你的package.json文件是否有效，可以运行`$ npm install`。

要是没有问题，就不会有任何输出内容，否则会抛出JSON异常的错误

![](https://github.com/Lumnca/Node.js/blob/master/img/a11.png)

接着，我们要创建一个简单的包含程序完整功能的JavaScript文件：index.js。

* 2.同步还是异步

我们从声明依赖关系开始。由于stdioAPl是全局process对象的一部分，所以，我们的程序唯一的依赖就是fs模块：

```js
var fs = require('fs');
```

我们首先要做的就是获取当前目录的文件列表。

有件重要的事情要记住：fs模块是唯一一个同时提供同步和异步API的模块。举个例子来说，要想获取当前目录的文件列表，可以使用如下方式：

![](https://github.com/Lumnca/Node.js/blob/master/img/a12.png)

它会立刻返回内容或者当有错误发生时抛出相应异常。

下面是异步：

```js
var fs = require('fs');

fs.readdir('.',function(err,files){
    console.log(files);
})
```

两者得到的结果一致。

前面提过，要在单线程中创建能够处理高并发的高效程序，就得采用异步、事件驱动的程序。尽管本章中这个命令行程序并非此类型程序
（因为同一时间只会有一个人在读取文件），但是，为了学习Node.js中最重要也是最具挑战的部分，我们还是保持这种异步的代码风格。
为了获取文件列表，我们需要使用fs.readdir。我们提供的回调函数首个参数是一个错误对象（如果没有错误发生，该对象为nu11），
另外一个参数是一个files数组。

* 3.理解流

我们已经知道，console.1og会输出到控制台。事实上，console.1og内部做了这样的事情：它在指定的字符串后加上\n（换行）字符，并将其写到stdout流中。









