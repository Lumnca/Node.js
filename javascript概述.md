### :maple_leaf:javascript概述 ###

***

**:fallen_leaf:介绍**

JavaScript是`基于原型、面向对象、弱类型的动态脚本语言`。它从函数式语言中借鉴了一些强大的特性，如闭包和高阶函数，这也是JavaScript语言本身有意思的地方。
从技术层面上来说，JavaScript是根据ECMAScript语言标准来实现的。这里有一点非常重要：由于Node使用了V8的原因，其实现很接近标准，另外，它还提供了一些标准之
外实用的附加功能。换句话说，在Node中书写的JavaScript和浏览器上口碑不是很好的JavaScript有着重要的不同。另外，Node中你书写的绝大多数JavaScript代码都符
合Douglas Crockford在他那本著名的书
《JavaScript语言精粹》中提到的JavaScript语言的“精粹”。

**:fallen_leaf:JavaScript基础**

在学习node之前最好有一点js基础。也最好先学习js之后再来学习node。本章节会介绍一下所必须的js基础

* 类型

JavaScript类型可以简单地分为两组：基本类型和复杂类型。访问**基本类型**，访问的是值，而访问复杂类型，访问的是对值的引用。
基本类型包括`number`、`boolean`、`string`、`nu11`以及`undefined`。**复杂类型**包括`array`、`function`以及`object`。


基础类型：

```js
var a = 1;
var b = 'hello';
var c = "xxx"   字符串不区分单双引号
var d = true;
```

复杂类型：

```js
  var arr = [1,"2",true];
    var obj = {name:'x',sex:'m'};
    var func = function(a){
        return a;
    }
```

复杂类型与基础类型的区别在于复杂类型的数据是对象化的数据存在，它有自己的一系列方法，属性。而且最大的特点就是复制这个值而是引用它，看如下实例：

```js
 var obj = {name:'x',sex:'m'};

    var obj2 = obj;
    obj2.name = 'a';

    console.log(obj1.name === obj2.name);  //ture
```

也就是说obj2与obj两个变量所指的空间都是同一个，而不是一个副本。对数组同样适用。

* 类型的困惑

要在JavaScript中准确无误地判断变量值的类型并非易事。因为对于绝大部分基本类型来说，JavaScript与其他面向对象语言一样有相应的构造器，比方说，
你可以通过如下两种方式来创建一个字符串：


```js
    var s1 = 'hello';
    var s2 = new String('world');
    console.log(s1+s2); //helloworld
```

然而，要是对这两个变量使用typeof和instanceof操作符，事情就变得有意思了：

```js
    var s1 = 'hello';
    var s2 = new String('world');

    console.log(typeof s1);  //string
    console.log(typeof s2);  //object
    console.log(s1 instanceof String);  //false
    console.log(s2 instanceof String);  //true
``` 

而事实上，这两个变量值绝对都是货真价实的字符串：

```js
a.substr==b.substr; //true
```
并且使用==操作符判定时两者相等，而使用===操作符判定时并不相同：

```js
a ==b; //true 
a ===b; //false
```


在条件表达式中，一些特定的值会被判定为false,nu11、undefined、''，还有0：

```js
     var a = 0;
     console.log(a==false);  //true
     a = -1;
     console.log(a==true);    //false
     a = 2;
     console.log(a==true);   //false
     a = 1;
     console.log(a==true);   //true
```

由上面的例子可以看出，在js中只有0代表false，1代表true，而不是非0数都为true。但实际上最好不要做这样的布尔判断，应为js有对于的boolean类型值。

还有就是null与undefined

```js
     var a;
     console.log(a);          //undefined
     console.log(typeof a);   //undefined
     console.log(typeof b);   //undefined（注意只能在typeof下使用没有声明的变量，直接使用没有声明的变量直接访问会报错）
```

undefined注重于没有定义该数据，或者没有初始化数据。

```js
    console.log(window.localStorage.getItem('name'));  //null
    console.log(document.getElementById('a'));         //null
```

null多用于获取对象数据不存在或者获取未初始化的对象数据。不等于没有定义的对象数据。也就是说null一般在用于获取也就是通过某些方法API去获取值而没有值
所导致的情况。另外还有：

```js
null == undefined  //true
null === undefined //false
```

由于null用于判断对象，它本身也是object类型数据：

```js
  var a = null;
  console.log(typeof a);  //object
  var b = a;
  a = {name:'s'};
  console.log(b);  //null
```

不同于其他的是b=a但是它们并不会指向同一个对象。因为null是没有数据的空间，所以两个变量无法指到同一个数据上，而是分开的对象变量数据。


* 函数

在JavaScript中，函数最为重要。它们都属于`一等函数`：可以作为引用存储在变量中，随后可以像其他对象一样，进行传递：

```js
  var a = function(){};
  console.log(a);
```

JavaScript中所有的函数都可以进行命名。有一点很重要，就是要能区分出函数名和变量名。

```js
var a = function a(){
 return 'function' == typeof a;//true
}
```

* this、function#call以及function#apply

this在函数中一般是指的全局对象window：

```js
var a = function a(){
 return window == this;//true
}
```

this变量所指的对象一般看作用域与在谁的身上。简单点来说就是指函数的上一级作用域的全局对象。由于外置一级函数的上一级作用域就是全局作用域所以代表的
是window，如果是对象内置函数就不一样了：

```js
  var a = {
        name : 's',
        getname : function(){
           return this.name;  
        }
    }
```

这里的this指的就是a对象本身。这是由于它是a的内置函数。可以使用apply方法为this赋值。

```js
    function func(){
        return  this.a == '123';
    }
    console.log(func.apply({a:'123'}));
```

ca11和apply的区别在于，ca11接受参数列表，而apply接受一个参数数组：

```js
    function func(a,b){
        return  a+b;
    }
    
    console.log(func.apply({func:'add'},[1,2]));
    console.log(func.call({func:'add'},3,4));
```

* 函数的参数数量
函数有一个很有意思的属性——参数数量，该属性指明函数声明时可接收的参数数量。在JavaScript中，该属性名为1ength：

```js
var a =function(a,b,c){};
a.length ==3；//true
```

尽管这在浏览器端很少使用，但是，它对我们非常重要，因为一些流行的Node.js框架就是通过此属性来根据不同参数个数提供不同的功能的。

有关更多的参数可以参考js语言，这里不再介绍。

* 闭包
在JavaScript中，每次函数调用时，新的作用域就会产生。
在`某个作用域中定义的变量只能在该作用域或其内部作用域`（该作用域中定义的作用域）中才能访问到：

```js
    var a = 1;
    function woot(){
        console.log(a);  //1
    }
    woot();
```

或者：

```js
var a = 1;
    function woot(){
        console.log(a);  1
        var b = 5;
        
        function foo(){
            var c = 2;
            console.log(b);   5 
        }
        console.log(typeof c);  //undefined
        foo(); 
    }
    console.log(typeof b);  //undefined
    woot();
```

可见只能在其作用域中访问。


自执行函数是一种机制，通过这种机制声明和调用一个匿名函数，能够达到仅定义一个新作用域的作用：


```js
    var a = 3;
    (function woot(){
        var a = 5;
    })();
    console.log(a); //3
```

自执行函数对声明私有变量是很有用的，这样可以让私有变量不被其他代码访问:

```js
(function woot(){
        var money = 5;
})();
console.log(typeof money); //undefined
```

* 类

JavaScript中没有class关键词。类只能通过函数来定义：

```js
function Animal (){

}
```

要给所有Animal的实例定义函数，可以通过prototype属性来完成：

```js
Animal.prototype.eat = function(){
    //实现吃的代码
}
```

值得一提的是，在prototype的函数内部，this并非像普通函数那样指向global对象，而是指向通过该类创建的实例对象：

```js
    function Animal (name){
        this.name = name;
    }

    Animal.prototype.eat = function(){
        //实现吃的代码
    }
    Animal.prototype.getName = function(){
        return this.name;
    }

    var a1 = new Animal('猴子');
    console.log(a1.getName());
```

* 继承

JavaScript有基于原型的继承的特点。通常，你可以通过以下方式来模拟类继承。定义一个要继承自Anima1的构造器：

```js
function Ferret(){}；
```

要定义继承链，首先创建一个Anima1对象，然后将其赋值给Ferret.prototype。

```js
Ferret.prototype = new Animal();
```

随后，可以为子类定义属性和方法：

```js
Ferret.prototype.type='domestic';
```

还可以通过prototype来重写和调用父类函数：

```js
    function Animal() {

    }
    Animal.prototype.eat = function (food) {
        //实现吃的代码
    }
    function Ferret() {
        
    }
    Ferret.prototype = new Animal();
    Ferret.prototype.type = 'domestic';
    Ferret.prototype.eat = function(food){
        Animal.prototype.eat.call(this,food);
        //Ferret特有的逻辑
    }
```

这项技术很赞。它是同类方案中最好的（相比其他函数式技巧），而且它不会破坏instanceof操作符的结果：

```js
    var a1 = new Animal();
    var a2 = new Ferret();
    console.log(a1 instanceof Animal);  //ture
    console.log(a2 instanceof Animal);  //ture
    console.log(a1 instanceof Ferret);  //false
    console.log(a2 instanceof Ferret);  //ture
```


它最大的不足就是声明继承的时候创建的对象总要进行初始化（Ferret.prototype=new Animal），这种方式不好。
一种解决该问题的方法就是在构造器中添加判断条件：


```js
    function Animal(a) {
       if(false !== a ) return;
       //初始化
    }
    Ferret.prototype = new Animal(false);
```


另外一个办法就是再定义一个新的空构造器，并重写它的原型：

```js
function Animal(){
//constructor stuff
}

function f (){}; 

f.prototype=Animal.prototype; 
Ferret.prototype=new f;
```

幸运的是，V8提供了更简洁的解决方案，本章后续部分会做介绍。

* try 与catch

try/catch允许进行异常捕获。当抛出错误时，代码就停止执行了。若使用try/catch则可以进行错误处理，并让代码继续执行下去：

```js
    try{
        var a = 5;
        a();
    }
    catch(e){
        console.log(e);
    }
```

* V8中的JavaScript

随着Chrome浏览器的发布，它带来了一个全新的JavaScript引擎——V8，它以极速的执行环境，加之时刻保持最新并支持最新ECMAScript特性的优势，快速地在
浏览器市场中占据了重要的位置。其中有些特性弥补了语言本身的不足。另外一部分特性的引入则要归功于像jQuery和PrototypeJS这样的前端类库，
因为它们提供了非常实用的扩展和工具，如今，很难想象JavaScript中要是没有了这些会是什么样子。
本章介绍V8中最有用的特性，并使用这些特性书写出更精准、更高效的代码，与此同时，代码的风格也将借鉴最流行的Node.js框架和库的代码风格。

* Object#key

要想获取下述对象的键（a和c）：

`var a={a:2,c:'s'}`通常会使用如下迭代的方式：

```js
for(var i in a){}
```

通过对键进行迭代，可以将它们收集到一个数组中。不过，如果采用如下方式对Object.prototype进行过扩展：
`object.prototype.c='d`为了避免在迭代过程中把c也获取到，就需要使用hasownProperty来进行检查：

 ```
for（var i in a）{
  if(a.hasownproperty(i)){
    //---
  }
}
```

在V8中，要获取对象上所有的自有键，还有更简单的方法：

```js
var a = {b:1,c:'s'}
Object.keys(a);  //["b","c"]
```

* 数组方法

正如你此前看到的，对数组使用typeof操作符会返回object。然而大部分情况下，我们要检查数组是否真的是数组。
`Array.isArray`对数组返回true，对其他值则返回false

要遍历数组，可以使用forEach:

```js
  var a = [5,7,8,6,1,2,12,45];

    a.forEach(e => {
        console.log(e);  
    });
```

要过滤数组元素，可以使用filter:

```js
var a = [5,7,8,6,1,2,12,45];
 a = a.filter((e)=>{
        return e%2==0;
 })
```

要改变数组中每个元素的值，可以使用map

```js
    var a = [5,7,8,6,1,2,12,45];

    a = a.map((e)=>{
       return e*2;
    });
    a.forEach(e => {
        console.log(e);  
    });
```

V8还提供了一些不太常用的方法，如reduce、reduceRight以及lastIndexOf。这里不过多介绍。可自行查阅。

* JSON

V8提供了JsoN.stringify和JsoN.parse方法来对JSON数据进行解码和编码。
JSON是一种编码标准，和JavaScript对象字面量很相近，它用于大部分的Web服务和API服务。

```js
    var a = {name:'ss',age:'x'};
    var s = JSON.stringify(a);  //对象转JSON字符串
    console.log(s);
    console.log(JSON.parse(s).name)  //JSON字符串转对象
```

* function bind

.bind 允许改变对this的引用：


```js
    function woot(){
        return this.a == '';123
    }

    var b =  woot.bind({a:'123'});
    b();  //true
```

V8还支持非标准的函数属性名：

```js
var a=function woot（）{}；a.name=='woot'；//true
```

该属性用于V8内部的堆栈追踪。当有错误抛出时，V8会显示一个堆栈追踪的信息，会告诉你是哪个函数调用导致了错误的发生.

V8无法为函数引用指派名字。然而，如果对函数进行了命名，v8就能在显示堆栈追踪信息时将名字显示出来.为函数命名有助于调试，因此，推荐始终对函数进行命名。

* _proto_

_proto_使得定义继承链变得更加容易：

```js
    function Animal(name) {
        this.name = name;
    }
   
    Animal.prototype.eat = function (food) {
        //实现吃的代码
    }
    Animal.prototype.getName = function () {
        return this.name;
    }

    function Ferret(name) {
       Animal.call(this,name);
    }

    Ferret.prototype.__proto__ = Animal.prototype;

    Ferret.prototype.type = 'domestic';
    Ferret.prototype.eat = function(food){
        Animal.prototype.eat.call(this,food);
        //Ferret特有的逻辑
    }
    var a1 = new Animal('s');
    var a2 = new Ferret('a');
```

这是非常有用的特性，可以免去如下的工作：
- 像上一章节介绍的，借助中间构造器。
- 借助OOP的工具类库。无须再引入第三方模块来进行基于原型继承的声明。


* 存取器

你可以通过调用方法来定义属性，访问属性就使用_defineGetter、设置属性就使用defineSetter。
比如，为Date对象定义一个称为ago的属性，返回以自然语言描述的日期间隔。很多时候，特别是在软件中，想要用自然语言来描述日期距离某个特定时间点的时间间隔。比如，“某件事情发生在三秒钟前”这种表达，远要比“某件事情发生在x年×月×日”这种表达更容易理解。
下面的例子，为所有的Date实例都添加了ago获取器，它会返回以自然语言描述的日期距离现在的时间间隔。简单地访问该属性就会调用事先定义好的函数，无须显式调用。


```js
    Date.prototype.__defineGetter__('ago',function(){
        var diff=(((new Date()).getTime()-this.getTime())/1000);
        var day_diff=Math.floor(diff/86400); 
        
        return day_diff==0 && (diff<60&&"just now"||diff<120&&"1minute ago" ||
        diff<3600&& Math.floor(diff/60)+"minutes ago" 
        || diff<7200&&"1 hour ago"
        || diff<86400 && Math. floor(diff/3600)+"hours ago") 
        || day_diff==1&&"Yesterday?"
        || day_diff<7 && day_diff+"days ago"
        ||  Math.ceil(day_diff/7)+"weeks ago";  
    });
    
    
    var y = new Date("2019/9/12")
    y.ago //"29weeks ago"
    var y = new Date("2020/3/28 8:00:00")
    y.ago //"4hours ago"
```

* 小结

理解掌握本章的内容对了解语言本身的不足以及大多数糟糕的JavaScript运行环境，如老版本的浏览器，至关重要。

由于JavaScript多年来自身发展缓慢且多少有种被人忽略的感觉，许多开发者投入了大量的时间来开发相应的技术以书写出更高效、可维护的JavaScript代码，同时也总结出了JavaScript一些诡异的工作方式。

V8做了一件很酷的事情，它始终坚定不移地实现最新版本的ECMA标准。Node.js的核心团队也是如此，只要你安装的是最新版本的Node，你总能使用到最新版本的V8。它开启了服务器端开发的新篇章，我们可以使用它提供的更易理解且执行效率更高的API。


