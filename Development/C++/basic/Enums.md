`enum` 默认使用 `int` 类型存储枚举值;
指定底层类型,可以改变存储方式;
可以降低

#### Example
```c++
#include <iostream>

enum test : unsigned char {
    VALUE1 = 1,
    VALUE2 = 2,
    VALUE3 = 3
};

int main() {
    test t = VALUE2;

    std::cout << "Value of t: " << static_cast<int>(t) << std::endl;

    return 0;
}

```