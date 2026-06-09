---
title: c++学习记录
category: 文档
tag: 学习
abbrlink: 49364
date: 2024-05-05 13:58:00
---

# 基础知识

## Lambda 表达式
- 基本格式
    1. capture_list: 捕获列表，用于捕获外部变量，可以为空。
        - [var]: 捕获指定变量 var 的值，以传值方式捕获。
        - [&var]: 捕获指定变量 var 的引用。
        - [=]: 以传值方式捕获所有局部变量。
        - [&]: 以引用方式捕获所有局部变量。
        - [=, &var]: 按值捕获所有变量，但是 var 除外，var 以引用方式捕获。
        - [&, var]: 按引用捕获所有变量，但是 var 除外，var 以传值方式捕获
    2. parameters: 参数列表，Lambda 表达式的参数列表，可以为空。
    3. return_type: 返回类型，Lambda 表达式的返回类型，可以省略（由编译器推导）。
```cpp
[capture_list](parameters) -> return_type {
    // Lambda 函数体
}
```
1. Lambda 表达式不捕获任何外部变量，无参数，无返回值
```cpp
[]() {
    std::cout << "Hello, Lambda!" << std::endl;
}
```
2. Lambda 表达式捕获外部变量，有参数，无返回值：
```cpp
int x = 42;
auto lambda = [x](int y) {
    std::cout << "x + y = " << x + y << std::endl;
};
```
3. Lambda 表达式使用返回值：
```cpp
auto lambda = [](int x, int y) -> int {
    return x + y;
};
```
4. 使用自动推导类型
```cpp
auto lambda = [](auto x, auto y) {
    return x + y;
};
```

## 初始化列表释放指针所指向的内存
```cpp
#include <iostream>
using namespace std;

int main() {
    // 创建一个整数指针
        int* ptr = new int(42);
    // 使用 Lambda 表达式和初始化列表释放指针所指向的内存
    auto deleter = [ptr]() {
        cout << "释放内存: " << *ptr << endl;
        delete ptr;
    };
    // 调用 Lambda 表达式释放内存
    deleter();
    return 0;
}
```

## 使用 Lambda 表达式实现自定义比较函数

- std::sort 
    1. 是 C++ 标准库中的排序函数，它使用快速排序算法（Quicksort）或者归并排序算法（Merge Sort）进行排序，具体实现取决于编译器和标准库的实现。

    2. 快速排序（Quicksort）是一种常用的排序算法，它的平均时间复杂度为 O(n log n)，最坏情况下的时间复杂度为 O(n^2)。归并排序（Merge Sort）同样也是一种常用的排序算法，它的时间复杂度始终为 O(n log n)，但需要额外的空间来存储中间结果。

    3. 无论使用哪种算法，std::sort 都会对容器中的元素进行原地排序（In-place Sorting），也就是说，它会直接修改容器中的元素的顺序，而不会创建新的容器来存储排序结果

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

int main() {
    std::vector<int> nums = {3, 1, 4, 1, 5, 9, 2, 6};
    // 使用 Lambda 表达式作为自定义比较函数
    auto compare = [](const int a, const int b) {
        return a < b;
    };
    // 使用指针和 Lambda 表达式排序
    std::sort(nums.begin(), nums.end(), compare);
    // 打印排序后的结果
    for (int num : nums) {
        std::cout << num << " ";
    }
    std::cout << std::endl;
    return 0;
}
```
## 使用泛型 Lambda 表达式和返回类型推导
```cpp
#include <iostream>

int main() {
    // 创建一个整数指针
    int x = 10;
    int* ptr = &x;
    // 使用泛型 Lambda 表达式和返回类型推导
    auto deref = [](auto* p) -> decltype(auto) {
        return *p;
    };
    // 使用 Lambda 表达式解引用指针
    auto value = deref(ptr);
    // 打印解引用后的值
    std::cout << "解引用后的值为: " << value << std::endl;
    return 0;
}
```

## 基本技巧

# 指针

## 使用指针的好处
1. 动态内存分配： 指针允许在运行时动态分配内存，这使得程序能够更灵活地管理内存资源，避免了静态分配内存时可能出现的空间浪费或不足的问题。
直接访问内存地址： 指针可以直接访问内存地址，这对于一些特定的编程任务非常有用，比如底层系统编程、内存管理和数据结构实现等。
2. 传递参数： 使用指针作为函数参数可以避免在函数调用时复制大量的数据，提高了程序的运行效率。此外，指针参数还可以用于实现函数对传入参数的修改，达到函数多返回值的效果。
数据结构的实现： 指针在数据结构的实现中非常重要，比如链表、树等数据结构都是通过指针来连接各个节点的。
3. 虽然使用指针可能会增加代码的复杂度，但它们在某些情况下是必不可少的。例如，当处理大量数据或需要动态内存管理时，使用指针可以提高程序的性能和效率。然而，在编写简单的程序或者对指针不太熟悉的情况下，直接使用变量可能更容易理解和维护