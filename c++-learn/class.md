## c++ 类


### c++类接口
也就是类的声明(函数的声明或者类的声明放在.h的文件中)

```c++
//stock.h
#ifndef STOCK_H
#defind STOCK_H

class Stock{
    private:
        std::string company;
        long shares;
        double share_val;
        double total_val;
        void set_tot(){total_val = shares*share_val;} //或者下一行
    public:
        .
        .
        .
       Stock(string &co,long s, double s_v) //构建函数
       Stock() //默认构建函数
        ~Stock()  //析构函数
} 

/**
 *  在private 中声明函数即可 void set_tot();
 *   inline void Stock::set_tot(){ //通过inline 将函数由全局变成改文件的属性
 *       total_val = shares*share_val;
 *   }
 **/
#endif



//stock.cpp


#include "stock.h";
#include <string>

Sock::Stock(){
    company =  "default";
    share_val = 0;
    share_val = 0.0;
    total_val = 0.0; 
}

Stock::Stock(string &co,long s, double s_v){ //构造函数
    company = co;
    if(n<0){
        std::cerr << "error!"
        shares = 0
    }else
        shares = s;
    share_val = s_v;
    set_tot();
    
}

Stock::~Stock(){ //自建析构函数 
    std::cout << "bye i will be rush"
}


//main.cpp

#include "Stock.h"

int main (){

    Sock sock1();// 默认构建函数
    Sock sock2; //同上
    Sock sock3 = Sock("人大集团",21,500) // 自建构建函数
    Sock sock4("人大集团",21,500) //同上
    Sock sock5 = {"人大集团",21,500} //每个参数必须对齐
    Sock sock6{"人大集团",21,500} //同上
    Sock *sock = new Sock() //动态创建(堆中) 
    return 0
}
```

注意:
* 构造函数的行参不要与class属性名相同 否则会产生 name = name 的错误
* 除了new是同态构建,其他都是静态构建(如果要删除静态只能在赋值或者程序运行完毕才能删除)


###const问题

```c++
const Sockt socket  = Socket ("人大集团",21,500);
stock.show() // forbidden 


//只能在函数声明中修改show方法

class Stock{
    public:
    void show() const ;
}


//Stock.cpp

void Stock::show() const {

}
```
原因:
* 由于class调用的是同方法不同对象

