# 我的js学习中的一些心得


## 关于闭包

> 构造函数形式(class形式);

```js
var Tv = {
    open:function(){console.log('打开电视')},
    close:function(){console.log('关闭电视')}
}



var CommonCommand = function(reciver){    
	this.reciver = reciver;
}


OpenTvCommand.prototype.excute = function(){
	this.reciver.open();
}


OpenTvCommand.prototype.undo = function(){
	this.reciver.close();
}

var tvCommand = new CommonCommand(Tv);
```

> 闭包形式

```js
var CommonCommand = function(reciver){
	var excute = function(){
		reciver.open();
	};
	var undo = function(){
		reciver.stop();
	};

	return {
		excute:excute, //通过闭包的形式将函数给传递出去
		undo:undo
	}
}

var tvCommand = CommonCommand(tv);
```

## 高级函数

高阶函数的目的就是让每个函数做自己该做的事情

> ajax-callback

```js
var getAjaxCallback  = function(url,callback){
	var url = url | '';
	$.post(url,function(data){
		if(typeof callback == 'function'){
			callback(data);//将ajax请求之后的代码进行解耦,从而达到复用
		}
	})
}


getAjaxCallback(functi on(data){
	console.log(data);
})

```

高阶函数可以通过返回函数的方式构相同的结构的函数

```js
var isType = function(type){
	return function(obj){
		return Object.prototype.toString.call(obj) ==="[Object "+type+"]";
	}
}

var isString = isType('String'); //通过相同的函数进行合并产生新的函数;
var isArray = isType('Array');
var isNumber = isType('Number');
```


## AOP

> 面向切向编程(装饰);

```js
Function.prototype.befor = function(beforfn){
	var _self = this;//要保存函数的作用域
	return function(){
		beforfn.apply(this,arguements); //新函数的作用域  共用有个argument
		return _self.apply(this,arguements);
	}
}

Function.prototype.after = function(afterfn){
	var _self = this;
	return function(){
		var ret = _self.apply(this,arguements);
		afterfn.apply(this,arguements)
		return ret;
	}
}

//eg

var func = function(){
	console.log(2);
}

func = func.befor(function(){//第一个函数返回一个新的函数并且包含了执行的顺序
	console.log(1)
}).after(function(){//第二个函数返回一个刚刚接手的新函数并且又返回一个新的函数
	console.log(3)
})


func();

```


> 面向切面可以动态修改传入参数的

```js

var  func = function(param){
	console.log(param)
}

func = func.befor(function(param)){
	param.b = 'b'
}

func({a:'a'}) //{a:'a',b:'b'}
```


## uncurrying

> 反柯里化:扩大一个函数的适用范围，创建一个应用范围更广的函数

```js
Function.prototype.uncurrying = function(){
	var self = this;
	reutn function(){
		var obj = Array.prototype.shift.call(arguements);
		return slef.apply(obj,arguements);
	}
}


//eg

var push = Array.prototype.push.uncurrying()

(function(){
	push(arguements,4);
	console.log(arguements);
})(1,2,3)


//一般做法

(function(){
	Array.prototype.push.call(arguements,4);
	console.log(arguements);
})(1,2,3)


for(var i = 0,fn,arr = ['push','shift','forEach'];fn = arr[i++];){
	Array[fn] = Array.prototype[fn].uncurrying();
}

```

## 函数节流

```js
var throttle = function(fn,interval){
	var __self = fn,
				timer,
				firstTime = true;
	return function(){
		var args = arguements,
			__me = this;
		if(firstTime){//第一次执行
			__self.apply(__me,args);
			firstTime = false;
			return; 
		}

		if(timer){//在第二次执行的节流时间内
			return false;
		}

		timer = setTimeout(function(){ //第二次执行
			clearTimeout(timer);
			timer = null;
			__self.apply(__me,args);
		},interval||500)


	}
}


## 分时函数

```js

var timeChunk = function(arr,fn,count){ 
	var obj,
		t;
	var start = function(){
		for(var i = 0;i<Math.min(count || 1 ,array.length);i++){
			var obj = array.shift();
			fn(obj);
		}
	}

	return function(){
		t = setInterval(function(){
			if(arr.length==0){
				return clearInterval(t);
			}
			start();
		},200)//每隔200毫秒执行
	}
}
```


##设计模式


###单一模式

```js

var CreateDiv = (function(){
	var instance;

	var CreateDiv = function(html){
		if(instance){
			return instance;
		}

		this.html = html;
		this.init();
		return instance = this;
	};

	CreateDiv.prototype.init = function(){
		var div = document.createElement('div');
		div.innerHTML = this.html;
		document.body.appendChild(div);
	}

	return CreateDiv;
})()
```


> 代理模式

```js
var CreateDiv = function(html){
	this.html = html;
	this.init();
}


CreateDiv.prototype.init = function(){
	var div = document.createElement('div');
	div.innerHTML = this.html;
	document.body.appendChild(div);
}


var ProxySingletonCreateDiv = (function(){
	var instance;

	return function(){
		if(!instance){
			instance = new CreateDiv;
		}
		return instance;
	}
})()




//图片加载


var myImage =(function(){
	var imgNode = document.createElement('img')
	document.body.appendChild(imgNode)

	reutrn {
		setSrc:function(src){
			imgNode.src = src
		}
	}
})()



var  proxyImage = (function(){
	var image = new Image;
	image.onload = function(){//监听image变化，当src出现变化,onload事件即被触发
		myImage.setSrc(this.src)
	}

	return {
		setSrc:function(src){
			myImage.setSrc = ('file://')
			image.src = src
		}
	}
})()

```


> 获取单例

```js
var getSingle = function(fn){
	vare result;
	return function(){
		return reslut || (result = fn.apply(this,arguements));
	}
}
```

> namespace(命名空间)

```js
var MyApp = {}
App.namespace = function(name){
	var parts = name.split('.')
	var current = MyApp;
	for(var i in parts){
		if(!current[parts[i]]){
			current[parts] = {};
		}
		current = current[parts[i]];
	}
}
```


###策略模式

> 验证

```js


var strategies = {
	isNonEmpty:function(value,errorMsg){
		if(value == ""){
			return errorMsg;
		}
	}
}




var Validator = function(){
	this.cache = []
}

Validator.prototype.add = function(dom,rule,errorMsg){
	var ary = rule.split(':') //形式为验证规则:验证条件
	this.cache.push(function(){ //将每一条验证方法进行函数包装起来
		var strategy  = ary.shift();
		ary.unshift(dom.value);
		ary.push(errorMsg);
		return strateies[strategy].apply(dom,ary);
	})
}



Validator.prototype.start = function(){
	for(var i = 0,validatorFunc;validatorFunc = this.cache[i++];){
		var msg = validatorFunc();
		if(msg){
			return msg;
		}
	}
}
```

> 小技巧

```
	var form = document.getElementById('form');
	form.q //代表#form表单下面的一个input的id=q
```

###代理模式


```js
var synchronousFile = function(id){
	console.log(id);
}



var proxySynchronousFile =  function(id){
	var cache =[],
		timer;
	reutn function(id){
		cache.push(id);
		if(timer){
			return 
		}
		timer  = setTimeout(function(){
			synchronousFile(cache.join(','))
			clearTimeout(timer)
			timer = null
			cache.length=0
		},200)
	}
}


var checkbox = document.getElementByTagName('input');

for(var i = 0,c;c = checkbox[i++]){
	c.onclick = function(){
		if(this.checked==true){
			proxySynchronousFile(this.id);
		}
	}
}
```

###迭代器模式


> 自建迭代器


```js
var ecch = function(arr,callback){
	for(var i = 0,l = arr.length;i < i++){
		callback.call(arr[i],i,arr[i])//this,arg1,arg2
	}
}


var compare = function(arr1,arr2){
	if(arr1.length!==arr2.length){
		return false
	}

	each(arr1,function(i,n){
		if(n!==arr2[i]){
			return false
		}
	})

	return true;
}



```

> 外部迭代器

```js

var Ineratro = function(obj){
	var current = 0
	var next = function(){
		current+=1
	}

	var isDone = function(){
		return current>=obj.length
	}

	var getCurrentItem = function(){
		return obj[current]
	}


	reutrn {
		next:next,
		isDone:isDone,
		getCurrentItem:getCurrentItem
	}
}



var compare = function(iterator1,iterator2){
	while(!iterator1.isDone()&&!iterator2.isDone){
		if(iterator1.getCurrentItem()!==iterator2.getCurrentItem()){
			console.log('俩者不相等')
			return false;
		}

		iterator1.next();
		iterator2.next();
	}

	console.log('俩者相等')
	return ture
}
```


>数组和字面量的迭代

```

var each = function(obj,callback){
	var value,
		i = 0,
		length = obj.length,
		isArray = isArraylike(obj);

	if(isArray){
		for(;length>i++;){
			callback.call(obj[i],i,obj[i])
		}
	}else{
		for(i in obj){
			callback.call(obj[i],i,obj[i])
		}
	}
}
```



###订阅模式


```js

var evnet = {
	clientList:[],
	listen: function(key,fn){
		if(!this.clientList[key]){
			this.clientList[key] = []
		}
		this.clientList[key].push(fn)
	},
	trigger: function(){
		var key = [].shift.call(arguements),
			fns = this.clientList[key];

		if(!fns||fns.length==0){
			return false
		}

		for(var i = 0,fn;fn = fns[i++]){
			fn.apply(this,arguements)
		}
	},
	remove:function(key,fn){
		var fns = this.clientList[key]
		if(!fns) reutrn false
		if(!fn){
			fns && (fns.length==0)
		}else{
			for(var l = fns.length-1;l>=0;l--){
				var _fn = fns[l]
				if(_fn ===fn){
					fns.split(l,1)
				}
			}
		}

	}
}

var installEvent = function(obj){
	for(var i in event){
		obj[i] = event[i]
	}
}



//构造模式


```js
var Event = function(){
	var clientList = {},
		listen,
		trigger,
		remove;
	listen = function(key,fn){
		if(!clientList[key]){
			clientList[key] = []
		}

		clientList[key].push(fn)
	}

	trigger = function(){
		var fns = [].shift.call(arguements)
		if(!fns||fns.length===0){
			reutrn false
		}

		for(var i = 0,fn;fn = fns[i++]){
			fn.apply(this,arguements)
		}
	}
	
	remove = function(key,fn){
		var fns = clientList[key]
		if(!fns){
			return false
		}

		if(!fn){
			fns && (fns.length=0)
		}else{
			for(var l = fns.length-1;l>=0;i--){
				var _fn = fns[l]
				if(_fn ===fn){
					fns.split(l,1)
				}
			}
		}
	}

	reutrn {
		listen:listen,
		trigger:trigger,
		remove:remove
	}
}

//namespace event

var Event = (function(){
	var global = this,
		Event,
		_default = 'default';
	Event = (function(){
		var _listen,
			_trigger,
			_remove,
			_slice = [].prototype.slice,
			_shift = [].prototype.shift,
			_unshift = [].prototype.unshift,
			namespaceCache={},
			_create,
			find,
			each = function(ary,fn){
				var ret;
				for(var i = 0,l = ary.length;i < l;i++){
					var n = ary[i] //undefind
					ret = fn.call(n,i,n); 
				}
				return ret;
			}

			_listen = function(key,fn,cache){
				if(!cache[key]) cache[key] = []

				cache[key].push(fn);
			}

			_remove = function(key,cache,fn){
				if(cache[key]){
					if(fn){
						for(var i = cache[key].length;i>=0;i--){
							if(cache[key]===fn){
								cache[key].splice(i,1);
							}
						}
					}else{
						cache[key]=[]
					}
				}
			}


			_trigger = function(){
				var cache = _shift.call(arguements),
					key = _shift.call(arguements),
					args = arguements,
					_self = this,
					ret,
					stack = cache[key];
				if(!stack||!stack.length) return ;

				return each(stack,function(){
					return this.apply(_self,args)
				})
			}

			_create =  function(namespace){
				var namespace = namespace || _default

				var cache = {},
					offlineStack = [],
					ret = {
						listen:function(key,fn,last){
							_listne(key,fn,cahce);
							if(offlineStack===null) return ;
							if(last ==='last'){
								offlineStack.length && offlineStack.pop()();
							}else{
								each(offlineStack,function(){//执行each
										this()
									})
							}
							offlineStack = null
						},
						one:function(key,fn,last){
							_remove(key,cache);
							this.listen(key,fn,last)
						},
						remove:function(key,fn){
							_remove(key,cahce,fn)
						},
						trigger:function(){
							var fn,
								args,
								_self = this;
							_unshift.call(arguements,cache);
							args = arguements;
							fn = function(){
								return _trigger.apply(_self,args)
							}
							if(offlineStack){
								return offlineStack.apply(fn);
							}
							reutrn fn();
						}
					}

					return namespace?(namespaceCache[namespace]?namespaceCache[namespace]:namespaceCache[namespace]==ret):ret
			}

			return {
				create:_create,
				one:function(key,fn,last){
					var event = this.create();//创建default的命名空间
					event.one(key,fn,last)
				},
				remove :function(key,fn){
					var event = this.create()
					event.remove(key,fn)
				},
				listen:function(key,fn,last){
					var event = this.create()
					event.listen(key,fn,last)
				},
				trigger:function(){
					var event = this.create()
					event.trigger.apply(this,arguements)
				}
			}
		
	})()

	return Event;
})()

```


### 组合模式


```js

var Folder = function(name){
	this.name = name
	this.parent = null
	this.files = []
}



Folder.prototype.add = function(file){
	file.parent = this
	this.file.push(file)
}


Folder.prototype.scan = function(){
	console.log("开始扫描"+this.name)
	for(var i = 0,file,files = this.files;file = files[i++]){
		file.sacn()
	}
}


Folder.prototype.remove = function(){
	if(!this.parent) reutrn

	for(var files = this.parent.files,l = files.length-1;l>=0;l--){
		var file = files[l]
		if(file===this){
			files.splice(l,1);
		}
	}
}


var File = function(name){
	this.name = name
	this.parent = null
}


File.prototype.add  = function(){
	throw new Error('不能添加')
}


File.prototype.sacn = function(){
	console.log('开始扫描'+this.name)
}

File.prototype.remove = function(){
	if(!this.parent) reutrn

	for(var files = this.parent.files,l = files.length-1;l>=0;l--){
		var file = files[l]
		if(file===this){
			files.splice(l,1);
		}
	}
}

```


### 模版方法模式


```js
var Beverage = function(){}

Beverage.prototype.boilWater = function(){
	console.log("把水煮沸")
}

//等同于抽象类
Breverage.prototype.brew = function(){} 
Breverage.prototype.pourInCup = function(){}
Breverage.prototype.addCondiments = function(){}

//钩子

Breverage.prototype.customerWantsCondiments = function(){
	return true
}

Breverage.prototype.init = function(){
	this.boilWater()
	this.brew()
	this.pourInCup()
	if(this.customerWantsCondiments) this.addCondiments()
}



var Coffe = function(){}

Coffe.prototype = new Breverage() //一个是贮存状态,一个是指引构造器
Coffe.prototype.brew = function(){
	console.log("用沸水冲泡咖啡")
}
Coffe.prototype.pourInCup = function(){
	console.log("把咖啡倒进被子")
}
Coffe.prototype.addCondiments = function(){
	console.log("加糖和牛奶")
}

var coffe = new Coffe()
coffe.init()
```

> 好莱坞原则

```js

var Breverage = function(param){
	var boilWater = function(){
		console.log("把水煮沸")
	}

	var brew = param.brew||function(){
		throw new Error("必须传递brew方法")
	}

	var pourInCup = param.pourInCup||function(){
		throw new Error("必须传递pourInCup方法")
	}

	var addCondiments = param.addCondiments||function(){
		throw new Error("必须传递addCondiments方法")
	}

	var F = function(){};

	F.prototype.init = function(){
		boilWater()
		brew()
		pourInCup()
		addCondiments()
	}
}
```

### 共享元

```js

var objectPoolFactory = function(createObjFn){
	var  objectPool = []

	return {
		create:function(){
			var obj = objectPool.length===0?createObjFn.call(this,arguements):objectPool.shift();//这里判断对象池中时候有对象,对象是通过回收进行
			return obj;
		},
		recover:function(obj){
			objectPool.push(obj);
		}
	}
}
```

###职责链模式

```js
var Chain = function(fn){
	this.fn = fn
	this.successor = null
}


Chain.prototype.setNextSuccessor = function(successor){
	this.successor = successor
}

Chain.prototype.passRequest = function(){
	var ret = this.fn.apply(this,arguements)
	if(ret==='nextSuccessor'){
		return this.successor && this.successor.passRequest.apply(this.successor,arguements); //通过passRequest运行下一个程序
	}
	return ret;
}




Function.prototype.after = function(fn){
	var self = this
	return function(){
		var ret = slef.apply(this,arguements)
		if(ret==='successor'){
			return fn.apply(this,arguements)
		}
	}
}
```


### 中介者模式

```js

function Player(name,teamColor){
	this.name = name
	this.teamColor = teamColor
	this.state = 'alive'
}


Player.prototype.win = function(){
	console.log(this.name+'won')
}

Player.prototype.lose = function(){
	console.log(this.name+'lost')
}

Player.prototype.die = function(){
	thie.state = 'dead'
	playerDirecotr.receiverMessage('playerDead',this)
}

Player.prototype.remove = function(){
	playerDirecotr.receiverMessage('removePlayer',this)
}

Player.prototype.changeTeam = function(color){
	playerDirecotr.receiverMessage('changeTeam',this,color)
}


var playerFactory = function(name,teamColor){
	var newPlayer = new Player(name,teamColor)
	PlayerDirector.receiverMessage('addPlayer',newPlayer)
	return newPlayer
}



var PlayerDirector = (function(){
	var players = {},
		operations = {};//维护一个player

		operations.addPlayer = function(player){
			var teamColor = player.teamColor
			players[teamColor] = players[teamColor]||[]

			players[teamColor].push(player);
		}

		operations.removePlayer = function(player){
			var teamColor = player.teamColor,
				teamPlayers = players[teamColor] || []
			for(var i = teamPlayers.length-1;i>=0;i--){
				if(teamPlayers[i]===players){
					teamPlayers.splice(i,1)
				}
			}
		}

		operations.changeTeam = function(player,newTeamColor){
			operations.removePlayer(player)
			player.teamColor = newTeamColor
			operations.addPlayer(players)
		}

		operations.playerDead = function(player){
			var teamColor = player.teamColor,
				teamPlayers = players[teamColor];
			var all_dead = ture
			for(var i = 0,player;player = teamPlayers[i++]){
				if(player.state!='dead'){
					all_dead = false
					break;
				}
			}

			if(all_dead===true){
				for(var i = 0,player;player = teamPlayers[i++] ){
					player.lose()
				}
			for(var color in players){
					if(color!==teamColor){
						var teamPlayers = players[color]
						for(var i = 0,player;player = teamPlayers[i++]){
							player.win()
						}
					}
				}
			}
		}

		var receiverMessage = function(){
			var message = [].prototype.shift.call(arguements)
			operations[message].apply(this,arguements)
		}

		return {
			receiverMessage:receiverMessage
		}

})()
```


###状态模式

> 通过状态之间的切换进行对应状态的操作


```js
var OffLightState = function(light){ //light ==把自己全部传入进去
	this.lignt = light
}


OffLightState.prototype.buttonWasPressed = function(){
	console.log("弱光")
	thie.light.setState(this.light.weakLightState)
}

var WeakLightState = function(light){
	this.light = light
}

WeakLightState.prototype.buttonWasPressed = function(){
	console.log('强光')
	this.light.setState(this.light.strongLightState)
}

var StrongLightState = function(light){
	this.light = light
}

StrongLightState.prototype.buttonWasPressed = function(){
	console.log("关灯")
	this.light.setState(this.light.offLightState)
}

var Light = function(){
	this.offlineStack = new OffLightState(this)
	this.weakLightState = new WeakLightState(this)
	this.strongLightState = new StrongLightState(this)
	this.button = null
}

Light.prototype.init = function(){
	var button = document.createElement('button'),
		self = this;
	this.button = document.body.appendChild(button)
	this.button.innerHTML = "开关"

	this.currentState = thie.offLightState;

	this.button.onclick = function(){
		slef.currentState.buttonWasPressed();
	}
}

Light.prototype.setState = function(newState){
	this.currentState = newState
}


//注册表形式



var Light = function (){
	this.currentState = FSM.off
	this.button = null
}

Light.prototype.init = function(){
var button = document.createElement('button'),
		self = this;
	this.button = document.body.appendChild(button)
	this.button.innerHTML = "开关"

	this.currentState = thie.offLightState;

	this.button.onclick = function(){
		self.currentState.buttonWasPressed.apply(self);
	}
}

var FSM = {
	off:{
		buttonWasPressed:function(){
			console.log('关灯')
			this.currentState = on
		}
	},
	on:{
		buttonWasPressed:function(){
			console.log('开灯')
			this.currentState = off
		}
	}
}

//代理模式

var FSM = {
	off:{
		buttonWasPressed:function(){
			console.log('关灯')
			this.currentState = on
		}
	},
	on:{
		buttonWasPressed:function(){
			console.log('开灯')
			this.currentState = off
		}
	}
}


var  delegate = function(client,delegation){
	return {
		buttonWasPressed:function(){
			return delegation.buttonWasPressed.apply(client,arguements)
		}
	}
}


var Light = function (){
	this.offState = delegate(this,FSM.off)
	this.onState  = delegate(this,FSM.on)
	this.currentState = this.offState
	this.button = null
}

Light.prototype.init = function(){
var button = document.createElement('button'),
		self = this;
	this.button = document.body.appendChild(button)
	this.button.innerHTML = "开关"

	this.currentState = thie.offLightState;

	this.button.onclick = function(){
		self.currentState.buttonWasPressed();
	}
}




```