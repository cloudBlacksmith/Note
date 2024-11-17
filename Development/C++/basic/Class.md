### 类的定义
```c++
class ClassName

{

private:

    /* data */

public:
	
	ClassName(/* args */);  //构造函数声明
	
	~ClassName();           //析构函数声明
	
};

  

ClassName::ClassName(/* args */)    //构造函数定义

{

}

  

ClassName::~ClassName()             //析构函数定义

{

}
```

### 构造函数
#### 定义:
1. 初始化对象的数据成员
2. 函数名要与类名相同
3. 可以有参数
4. <font color="red">!!!不能有返回值!!!</font>
5. 一个类可以拥有多个构造函数,为重载关系
6. 在未进行构造函数定义时,编译器将生成默认无参构造函数
#### copy构造函数

>在已有一个类时,直接copy现有类的成员数据时使用
>(和普通的构造函数差不多,只是将数据来源更换为现有类)

#### 示例
```c++
#include <iostream>

#include <string>

#include <ctime>

  

using namespace std;

  

class ImplicitAndShowCalls

{

private:

    string* str_val;

    int* local_time;

  

public:

    ImplicitAndShowCalls(string* str, int* local_time) : str_val(str), local_time(local_time) {}    //普通构造函数

    ImplicitAndShowCalls(const ImplicitAndShowCalls& p);                                            //copy构造函数

    ~ImplicitAndShowCalls();

    string GetStr_val();

    int Getlocal_time();

};

  

ImplicitAndShowCalls::~ImplicitAndShowCalls() {}

  

ImplicitAndShowCalls::ImplicitAndShowCalls(const ImplicitAndShowCalls& p)

{

    this->str_val = p.str_val;

    this->local_time = p.local_time;

}

  

string ImplicitAndShowCalls::GetStr_val()

{

    return *str_val;

}

  

int ImplicitAndShowCalls::Getlocal_time()

{

    return *local_time;

}

  

int main(int argc, char const* argv[])

{

    const char* chr = "Hi, My name is Fonzo";

    string* str = new string(chr);

    time_t nowtime;

    time(&nowtime);

    int local_time = static_cast<int>(nowtime);

    ImplicitAndShowCalls c(str, &local_time);

    ImplicitAndShowCalls c1(c);

    cout << "c " << c.GetStr_val() << ' ' << c.Getlocal_time() << endl;

    cout << "c1 " << c1.GetStr_val() << ' ' << c.Getlocal_time() << endl;

    delete str;

    return 0;

}
```

>运行可以发现 `c1` 与 `c` 的成员变量值相同

### 析构函数
#### 定义:
1. 析构函数没有参数,也没有类型;
2. 析构函数将在对象销毁时自动被调用;
3. 析构函数时对象释放系统资源的保障;
示例:
```c++

```
[[Free or Delete]]