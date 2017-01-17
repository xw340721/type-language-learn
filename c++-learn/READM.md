#c++学习


## 编译

> CC(cfront)编译器

```terminal
CC main.C //注意都是大写的C 生成main.o 最后和系统的C文件合并生成 a.out的执行文件
CC my.c precious.c //编译my和precious 文件
CC my.c precious.o //修改了my文件 但是percious没修改可以连接.o文件;
CC usingmath -lm //显示需求一些函数 -lm为数学库中定义的函数

```

> g++编译器(unix)

```terminal
g++ spiffy.cxx //同上
g++ spiffy.cxx -lg++ //同上
g++ spiffy.cxx precious.c 
g++ spiffy.cxx precious.o
```

> g++编译器(windows)

```terminal
g++ spiffy.cpp //同上
g++ spiffy.cpp -lg++ //同上
g++ spiffy.cpp precious.c 
g++ spiffy.cpp precious.o
```

小技巧

* 在main函数的return前面加 cin.get()--获取键盘按键 可以让代码不执行完毕后退出 

### using namespace 

由于c++中引入namespace,所以函数的调用一般要加namespace::method();

```c++

int main{
	using namespace std;
	cout << "hello world";
}

```


```c++

int main{
	std::cout << "hello world";
}

```

注意:

* #include 只是编译器的拼接的指引(a perprocessor directive) 不会引入namesapce

* cout 中 << 表示插入out流

* 在文件头部使用using namespace 表示整个文件在某一个namesapce当中

* 在函数中使用using namespace 表示在这个函数中将namespace当做call方法的简洁写法 

### 初始值的赋值

c++中采用{}进行初始值的赋值

```c++

int first{1};
int first = {1};
int zero = {};//赋值为0

```

### cout输出不同进制数值


* 10进制 dec(decimal)

* 8进制  oct(octonary)

* 16进制 hex (hexadecimal)


```c++

int main(){
	int first = 1;
	int second = 02;
	int third = 0x03;
	using namespace std;		
	cout << dec;
	cout << first << endl;
	cout << oct;
	cout << second << endl;
	cout << hex;
	cout << third << endl;
	cin.get();
	return 0;
}

```


### 指针学习


注意事项:

* 指针在声明的时候初始化

```c

int* a;

*a = 1000000000000000000 //由于没有初始化指针地址,*a赋值代表将要赋值的地址取给a地址从而出现错误


```


* c++中的指针代表的是某某类型的指针(c中经常指的是改指针的类型为某某类型)

```c
int* a;
int *a;
```

* c++和c初始化指针不同

```c
//c++
int* a = new int;//typeName * pointer_name = new typeName;
int* a  = (int *) malloc(sizeof(int));

//c

int *a = 10 ;


## const int * a 和 int * const a 区别

在c++中const int * a  可以赋值给非const 类型

```c++

int a = 10;
const int b = 20;
const int *pointer  = &a; //代表这个pointer指针为指定a的地址 = int * const pointer
const int *pointer2 = &b; //代表指向一个为const int的对象的指针 = const int * pointer