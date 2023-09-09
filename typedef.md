# typedef定义函数指针

`typedef`关键字可以用于定义函数指针类型，从而简化使用函数指针的代码。下面是一个使用`typedef`关键字定义函数指针类型的例子：

```c++
typedef int (*FunctionPtr)(int, int);
```

在上面的代码中，我们定义了一个名为`FunctionPtr`的函数指针类型，该函数指针指向一个参数为两个整型变量的函数，并且该函数返回一个整型值。具体来说，该类型定义可以解读为：

1. `FunctionPtr`是一个函数指针类型的名称，可以用来声明或定义函数指针变量。
2. `int`表示该函数返回一个整型值。
3. `*`表示该类型是一个指针类型。
4. `(int, int)`表示函数的参数列表，其中第一个`int`表示第一个参数的类型，第二个`int`表示第二个参数的类型。

现在，我们可以使用`FunctionPtr`类型定义函数指针变量，例如：

```c++
int add(int a, int b) {
    return a + b;
}

int main() {
    FunctionPtr f = add;
    int result = f(1, 2);
    return 0;
}
```

在上面的代码中，我们定义了一个名为`add`的函数，该函数接受两个整型参数，并返回它们的和。然后，在`main`函数中，我们声明了一个名为`f`的`FunctionPtr`类型的函数指针变量，并将其指向`add`函数。最后，我们使用该函数指针变量来调用`add`函数，并将结果存储在`result`变量中。

总之，`typedef`关键字可以用于定义函数指针类型，从而简化使用函数指针的代码。使用该类型定义可以使代码更加易读易懂，并提高代码的可维护性。





# using定义函数指针

```c++
using FunctionPtr = void (*)(int);  // 使用using定义函数指针类型

void MyFunction(int value)
{
    // 函数实现
}

int main()
{
    FunctionPtr ptr = &MyFunction;  // 声明并初始化函数指针变量

    // 通过函数指针调用函数
    ptr(42);

    return 0;
}
```