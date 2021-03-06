---
published: true
tags:
  - C++ 11
author: persuez
---

### explict 关键字
C++ 11 之前，expicit 关键字用在拥有单个非默认参数的构造函数之前，明确指示该构造函数不能用于隐式转换和复制初始化。如：
```
class P
{
public:
  P(int a){}
  P operator+(const P &){}
};
```
对于上面类 P 的定义，我们可以进行如下操作：
```
P p1(1); // ok
P p2 = p1 + 2; // ok, 将 2 通过 P(2)构造函数隐式转换成为 P 类型。  
```
但是如果我们不需要这种隐式转换，则可以使用explicit，表示这个构造函数只能被显示调用：
```
class P
{
public:
  explicit P(int a){}
  P operator+(const P &){}
};
```
这样一来，再进行上面的操作，将会报错。
上面就是C++ 11 之前的 explicit，在 C++ 11 中，explicit 关键字不仅可以用在单一参数的构造函数中，还可以用在多个参数的构造函数和转换函数（重载类型）。如（下面例子来自https://zh.cppreference.com/w/cpp/language/explicit）：
```
struct A
{
    A(int) { }      // 转换构造函数
    A(int, int) { } // 转换构造函数 (C++11)
    operator bool() const { return true; }
};

struct B
{
    explicit B(int) { }
    explicit B(int, int) { }
    explicit operator bool() const { return true; }
};

int main()
{
    A a1 = 1;      // OK ：复制初始化选择 A::A(int)
    A a2(2);       // OK ：直接初始化选择 A::A(int)
    A a3 {4, 5};   // OK ：直接列表初始化选择 A::A(int, int)
    A a4 = {4, 5}; // OK ：复制列表初始化选择 A::A(int, int)
    A a5 = (A)1;   // OK ：显式转型进行 static_cast
    if (a1) ;      // OK ：A::operator bool()
    bool na1 = a1; // OK ：复制初始化选择 A::operator bool()
    bool na2 = static_cast<bool>(a1); // OK ：static_cast 进行直接初始化

//  B b1 = 1;      // 错误：复制初始化不考虑 B::B(int)
    B b2(2);       // OK ：直接初始化选择 B::B(int)
    B b3 {4, 5};   // OK ：直接列表初始化选择 B::B(int, int)
//  B b4 = {4, 5}; // 错误：复制列表初始化不考虑 B::B(int,int)
    B b5 = (B)1;   // OK ：显式转型进行 static_cast
    if (b2) ;      // OK ：B::operator bool()
//  bool nb1 = b2; // 错误：复制初始化不考虑 B::operator bool()
    bool nb2 = static_cast<bool>(b2); // OK ：static_cast 进行直接初始化
}
```

---
### range-based for
这是一个很小的知识点，直接看例子：
```
for (auto i : {1,2,3,4}) {
  cout << i << endl;
  // 值传递
}
vector<string> vec;
...
for (auto &i : vec) {
  ...
  // 可能会导致 vec 中的元素改变，因为是引用传值
}
```
