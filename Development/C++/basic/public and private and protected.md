# 基类

| 访问类型      | 含义                                                              |     |
| --------- | --------------------------------------------------------------- | --- |
| private   | 声明为 **`private`** 的类成员只能由类的成员函数和友元（类或函数）使用。                     |     |
| protected | 声明为 **`protected`** 的类成员可由类的成员函数和友元（类或函数）使用。 此外，它们还可由派生自该类的类使用。 |     |
| public    | 声明为 **`public`** 的类成员可由任意函数使用。                                  |     |

>通过访问控制可以将类的 `public` 接口和 `private` 实现详细信息和仅供派生类使用的 `protected` 成员分离开;

## 示例:
```c++
class Point
{
public:
    Point( int, int ) // Declare public constructor.;
    Point();// Declare public default constructor.
    int &x( int ); // Declare public accessor.
    int &y( int ); // Declare public accessor.

private:                 // Declare private state variables.
    int _x;
    int _y;

protected:      // Declare protected function for derived classes only.
    Point ToWindowCoords();
};
```

# 派生类中的成员访问
两个因素控制基类的哪些成员可在派生类中访问；这些相同的因素控制对派生类中的继承成员的访问：

- 派生类是否使用 **`public`** 访问说明符声明基类。
    
- 基类中对成员的访问权限如何。

## 基类中的成员访问;

|**`private`**|**`protected`**|**`public`**|
|---|---|---|
|始终无法通过任何派生访问进行访问|如果使用 **`private`** 派生，则在派生类中为 **`private`**|如果使用 **`private`** 派生，则在派生类中为 **`private`**|
||如果使用 **`protected`** 派生，则在派生类中为 **`protected`**|如果使用 **`protected`** 派生，则在派生类中为 **`protected`**|
||如果使用 **`public`** 派生，则在派生类中为 **`protected`**|如果使用 **`public`** 派生，则在派生类中为 **`public`**|

### 示例:
```c++
// access_specifiers_for_base_classes.cpp
class BaseClass
{
public:
    int PublicFunc(); // Declare a public member.
protected:
    int ProtectedFunc(); // Declare a protected member.
private:
    int PrivateFunc(); // Declare a private member.
};

// Declare two classes derived from BaseClass.
class DerivedClass1 : public BaseClass
{
    void foo()
    {
        PublicFunc();
        ProtectedFunc();
        PrivateFunc(); // function is inaccessible
    }
};

class DerivedClass2 : private BaseClass
{
    void foo()
    {
        PublicFunc();
        ProtectedFunc();
        PrivateFunc(); // function is inaccessible
    }
};

int main()
{
    DerivedClass1 derived_class1;
    DerivedClass2 derived_class2;
    derived_class1.PublicFunc();
    derived_class2.PublicFunc(); // function is inaccessible
}
```