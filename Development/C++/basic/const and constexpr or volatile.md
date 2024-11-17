>[!note] 
>
>1. `const` 可以在运行时初始化;
>2. `constexpr` 只能在编译时初始化;
>3. `volatile` 变量不做任何优化;每次都需要进行访问;

## `const` and `constexpr`
### 对于指针

#### const
>`const` 在 `*` 左边表示该指针指向的对象为一个常量
>在 `*` 的右边表示该指针为一个常量

#### constexpr
> `constexpr` 仅仅对指针本身有效;
> 编译器可以针对 `constexpr` 做很大的优化,例如：将用到的`constexpr`表达式直接替换成结果, 相比宏来说没有额外的开销。

#### 示例
```c++
#include <iostream>
using namespace std;
 
int g_tempA = 4;
const int g_conTempA = 4;
constexpr int g_conexprTempA = 4;
 
int main(void)
{
	int tempA = 4;
	const int conTempA = 4;
	constexpr int conexprTempA = 4;
	/*1.正常运行，编译通过*/
	const int &conptrA = tempA;
	const int &conptrB = conTempA;
	const int &conptrC = conexprTempA;
 
	/*2.有两个问题：一是引用到局部变量，不能再编译器确定；二是conexprPtrB和conexprPtrC应该为constexpr const类型，编译不过*/
	constexpr int &conexprPtrA = tempA;
	constexpr int &conexprPtrB = conTempA;
	constexpr int &conexprPtrC = conexprTempA;
 
	/*3.第一个编译通过，后两个不通过，原因是因为conexprPtrE和conexprPtrF应该为constexpr const类型*/
	constexpr int &conexprPtrD = g_tempA;
	constexpr int &conexprPtrE = g_conTempA;
	constexpr int &conexprPtrF = g_conexprTempA;
 
	/*4.正常运行，编译通过*/
	constexpr const int &conexprConPtrD = g_tempA;
	constexpr const int &conexprConPtrE = g_conTempA;
	constexpr const int &conexprConPtrF = g_conexprTempA;
 
	return 0;
}
```

##### PS:有一个奇葩的地方就是可以通过上例`conexprPtrD`来修改`g_tempA`的值，也就是说`constexpr`修饰的引用不是常量

### volatile

>表示被修饰的对象可能在程序之外被修改,告知编译器不做任何优化,因为该对象的值可能随时发生变化;
>多用于硬件寄存器访问,多线程编程(信息同步,数据一致)以及信号处理等;