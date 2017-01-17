# new 代替代码

#### 通过创建函数模拟new过程

```js
function Person(name){
	this.name = name;
}

var a = new Person('name');


var createFactory = function(){
	var obj = new Object(),
			  Construct = [].shift.call(arguments) //获取构建函数 ;
	obj.__proto__ = Construct.prototype;
	var ret = Construct.call(obj,arguments);
	return typeof ret === "object" ? ret : obj;
}

var a = createFactory(Person , 'name')
```

在看看php中的传统class构建的方式

```php

class Person{
	private $name;
	function __construct($name){
		$this->name = $name;
	}
}

$xiao = new Person('name');
```


> 
 * 从中可以看到一个函数的构建是通过obj构建出来的,而obj是从obj.prototypey的对象克隆出来的;
 * \_\_proto\_\_是一个指向构造器的属性;
 * prototype即父类也是子类克隆的对象;
 * 属性的寻找过程为: 子类.属性->子类.\_\_proto\_\_(构造器)->父类.属性;	



#### bind代码实现


```js
Function.prototype.bind = function(contenxt){
	var self = this, //保留原函数
		context = [].shift.call(arguments),
		args = [].slice.call(arguments);
	return function(){
		return self.apply(context,[].concat.call(args,[].slice.call(arguments)); //传入context(上下行文)
	} 
}

```

=======


	