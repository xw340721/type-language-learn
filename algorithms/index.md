## struct 和 typedef struct区别

* struct 是申明一个结构
* typedef  struct 声明结构体

```c++

typedef struct tagMyStruct
　　　　{ 
　　　　　int iNum;
　　　　　long lLength;
　　　　} MyStruct,*myStruct;
```
这完成俩个操作:

* 1.定义一个新的结构类型

```c

struct tagMyStruct
　　　　{　　 
　　　　　int iNum; 
　　　　　long lLength; 
　　　　}MyStruct,*myStruct;
```

* 2.声明结构体

* 3.不用typedef，声明时必须struct MyStruct myStruct;用typedef，声明时就可以MyStruct myStruct.

* 4.链式指针的header及链式的第一个元素