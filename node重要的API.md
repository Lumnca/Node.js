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

![](https://github.com/Lumnca/Node.js/blob/master/img/a13.png)

process全局对象中包含了三个流对象，分别对应三个UNIX标准流：

```
-**stdin**：标准输入
-**stdout**：标准输出
-**stderr**：标准错误
```

![](https://github.com/Lumnca/Node.js/blob/master/img/a14.png)

第一个stdin是一个可读流，而stdout和stderr都是可写流。

stdin流默认的状态是暂停的（paused）。通常，执行一个程序，程序会做一些处理，然后退出。不过，有些时候，就像本章中的这个应用一样，程序需要一直处
在运行状态来接收用户输入的数据。

当恢复那个流时，Node会观察对应的文件描述符（在UNIX下为0），随后保持事件循环的运行，同时保持程序不退出，等待事件的触发。除非有10等待，否则Node.js总是会自动退出。

流的另外一个属性是它默认的编码。如果在流上设置了编码，那么会得到编码后的字符串（utf-8、ascii等）而不是原始的Buffer作为事件参数。
Steam对象和EventEmitter很像（事实上，前者继承自后者）。在Node中，你会接触到各种类型流，如TCP套接字、HTTP请求等。简而言之，当涉及持续不断地对数据进行读写时，流就出现了。

**输入和输出**

既然已经知道运行程序后大概是怎样的一个情形了，我们来尝试写第一部分，列出当前目录下的文件，然后等待用户输入：

```js
var fs = require('fs');
var process = require('process');
fs.readdir(process.cwd(), function (err, files) {
    console.log(' ');
    if (!files.length) {
        return console.log('\033[31m No files to show!\033[39m\n');
    }

    console.log(' Select which file or directory you want to see\n');

    function file(i) {
        var filename = files[i];
        fs.stat(__dirname + '/' + filename, function (err, stat) {
            if (stat.isDirectory()) {
                console.log(+i + '\033[36m' + filename + '/\033[39m');
            }
            else {
                console.log(+i + '\033[90m' + filename + '\033[39m');
            }
            i++;
            if (i == files.length) {
                console.log(); 
                process.stdout.write('\033[33mEnter your choice:\033[39m'); 
                process.stdin.resume();

            } else {
                file(i);
            }
        });
    };
    file(0)
});
```


如果files数组为空，告知用户当前目录没有文件。文本周围的`\033[31m`和`\033[39m`是为了让文本呈现为红色。例子中最后一个字符又是换行符\n，也是为了输出更友好。

```js
 if (!files.length) {
        return console.log('\033[31m No files to show!\033[39m\n');
}
```

紧接着，定义了一个函数，数组中每个元素都会执行该函数。这里也出现了贯穿本书始终的第一种异步流控制模式：串行执行。本章最后会对此做详细介绍。


然后，先获取文件名，再查看文件名对应路径的情况。fs.stat会给出文件或者目录的元数据:

```js
fs.stat(__dirname + '/' + filename, function (err, stat) {
    //...
});
```

回调函数中，同时还给出了错误对象（如果有的话）和一个Stat对象。本例中所使用到的Stat对象上的方法是isDirectory：

```js
            if (stat.isDirectory()) {
                console.log(+i + '\033[36m' + filename + '/\033[39m');
            }
            else {
                console.log(+i + '\033[90m' + filename + '\033[39m');
            }
```

如果路径所代表的是目录，我们就用有别于文件的颜色标识出来。
接下来就到了流控制中的核心部分了。计数器不断递增，与此同时，检查是否还有未处理的文件：

```js
            i++;
            if (i == files.length) {
                console.log(''); 
                process.stdout.write('\033[33mEnter your choice:\033[39m'); 
                process.stdin.resume();

            } else {
                file(i);
            }
```

如果所有文件都处理完毕，此时提示用户进行选择。注意，这里使用的是process.stdout

write而不是console.1og；这样就无须换行，让用户可以直接在提示语后进行输入

下面这行代码，此前介绍过，是用户等待用户输入：

```js
 process.stdin.resume();
```

紧跟着这行代码的是设置流编码为utf8，这样就能支持特殊字符了：`process.stdin.setEncoding（"utf8）；`

要是还有未处理的文件，则递归调用该函数来进行处理`file(i)`

**重构**

要做重构，我们从为几个常用的变量（如stdin和stdout）创建快捷变量开始：

```js
var fs = require('fs');
var process = require('process');
var stdin = process.stdin;
var stdout = process.stdout;
```

由于我们书写的代码都是异步的，因此，会有这样的问题：随着函数量的增长（特别是流控制层的增加），过多的函数嵌套会让程序的可读性变差。
为了避免此类问题，我们可以为每一个异步操作预先定义一个函数。
首先，我们抽离出一个读取stdin函数：

```js
 if (i == files.length) {
   read();
 } else {
   file(i);
 }
            
            
function read(){
    console.log(''); 
    stdout.write('\033[33mEnter your choice:\033[39m'); 
    stdin.resume();
    stdin.setEncoding('utf8');
}
```

注意，上述代码所使用的是新的stdin和stdout引用。
读取用户输入后，接下来要做的就是根据用户输入做出相应处理。用户需要选择要读取的文件，所以，代码层面，我们设置了stdin的编码后，就开始监听其data事件：

```js
    stdin.on('data', function (data) {
        if (!files[Number(data)]) {
            stdout.write('\033[3lmEnter your choice:\033[39m');
        } else {
            stdin.pause();
        }
    });
```

如果检查通过，我们要确保再次将流暂停（回到默认状态），以便于之后做完fs操作后，程序能够顺利退出:

![](https://github.com/Lumnca/Node.js/blob/master/img/a15.png)

至此，我们的程序已经能够与用户进行交互了，将当前目录中的文件列表展现给用户，下面我们来实现读取和显示文件内容。

**用fs进行文件操作**

```js
          stdin.on('data', function (data) {
                if(!files[Number(data)]) {
                    stdout.write('\033[3lmEnter your choice:\033[39m');
                } else {
                    stdin.pause();
                    fs.readFile(__dirname + '/' + filename,'utf8',function(err,data){
                        console.log(''); 
                        console.log('\033[90m'+data.replace(/(.*)/g,'    $1')+'\033[39m');
                    });
                }
            });
```


再次提醒，我们可以事先指定编码，这样我们得到的数据就是相应的字符串了：:

```js
 fs.readFile(__dirname + '/' + filename,'utf8',function(err,data)
```

接着，可以使用正则表达式添加一些辅助缩进后将文件内容进行输出:

```js
  console.log('\033[90m'+data.replace(/(.*)/g,'    $1')+'\033[39m');
```

![](https://github.com/Lumnca/Node.js/blob/master/img/a16.png)

不过，要是选择的是目录呢？这种情况下，我们就应当将其目录下的文件列表显示出来。为了避免再次执行fs.stat，我们在fi1e函数中，将Stat对象保存下来：


```js
 							if(stats[Number(data)].isDirectory()){
                    fs.readdir(__dirname+'/'+filename, function (err, files){
                        console.log(''); 
                        console.log('('+files. length+' files)'); 
                        files.forEach(function(file){
                            console.log('     -'+file);
                        }); 
                        console.log('');
                    });
                }
                else{
                    fs.readFile(__dirname + '/' + filename,'utf8',function(err,data){
                        console.log(''); 
                        console.log('\033[90m'+data.replace(/(.*)/g,'    $1')+'\033[39m');
                    });
                }
```

到这里我们就完成了首个Node命令行（CLI）程序。完整代码：

```js
var fs = require('fs');
var process = require('process');
var stdin = process.stdin;
var stdout = process.stdout;
var stats = [];
fs.readdir(process.cwd(), function (err, files) {
    console.log(' ');

    if (!files.length) {
        return console.log('\033[31m No files to show!\033[39m\n');
    }

    console.log(' Select which file or directory you want to see\n');

    function file(i) {
        var filename = files[i];
        fs.stat(__dirname + '/' + filename, function (err, stat) {
            stats[i] = stat;
            if (stat.isDirectory()) {
                console.log(+i + '\033[36m' + filename + '/\033[39m');
            }
            else {
                console.log(+i + '\033[90m' + filename + '\033[39m');
            }
            i++;
            if (i == files.length) {
                read();
            } else {
                file(i);
            }
            
        });
    };
    file(0);
    function read() {
        console.log('');
        stdout.write('\033[33mEnter your choice:\033[39m');
        stdin.resume();
        stdin.setEncoding('utf8');
        stdin.on('data', function (data) {
            var filename = files[Number(data)];
            if(!files[Number(data)]) {
                stdout.write('\033[3lmEnter your choice:\033[39m');
            } else {
                stdin.pause();
                if(stats[Number(data)].isDirectory()){
                    fs.readdir(__dirname+'/'+filename, function (err, files){
                        console.log(''); 
                        console.log('('+files. length+' files)'); 
                        files.forEach(function(file){
                            console.log('     -'+file);
                        }); 
                        console.log('');
                    });
                }
                else{
                    fs.readFile(__dirname + '/' + filename,'utf8',function(err,data){
                        console.log(''); 
                        console.log('\033[90m'+data.replace(/(.*)/g,'    $1')+'\033[39m');
                    });
                }
                
            }
        });
    }
});
```




