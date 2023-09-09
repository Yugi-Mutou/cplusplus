# bind和function

在C++中，`std::bind`和`std::function`是两个用于处理函数和函数对象（包括lambda表达式）的实用工具。它们都是C++11标准引入的，位于`<functional>`头文件中。

## bind

`std::bind`用于**将可调用对象与其参数一起绑定，形成一个新的可调用对象**。例如，我们可以用`std::bind`来创建一个调用特定函数的新函数，该函数自动使用某些特定参数调用原函数。

这是一个`std::bind`的例子：

```c++
#include <iostream>
#include <functional>

void print_sum(int x, int y) {
    std::cout << x + y << '\n';
}

int main() {
    // 使用 std::bind 创建一个新的函数对象，这个函数对象会调用 print_sum，并将第一个参数设置为 10
    auto sum_with_ten = std::bind(print_sum, 10, std::placeholders::_1);
    
    // 当我们调用 sum_with_ten 时，它会调用 print_sum，将 10 和我们传给 sum_with_ten 的参数一起作为参数
    sum_with_ten(5); // prints 15

    return 0;
}
```

在这个例子中，`std::bind(print_sum, 10, std::placeholders::_1)`创建了一个新的可调用对象。这个对象在被调用时会调用`print_sum`函数，并将第一个参数设置为10，将第二个参数设置为传给新创建的可调用对象的参数。

`std::placeholders::_1`是一个占位符，代表新创建的函数对象的第一个参数。如果有多个参数，你可以使用`std::placeholders::_2`，`std::placeholders::_3`等等。





## function

`std::function`则是一个通用的函数包装器。它**可以持有任何可以调用的对象**，例如函数指针，函数对象，lambda表达式，bind创建的对象以及重载了函数运算符的类。

这是一个`std::function`的例子：

```c++
#include <iostream>
#include <functional>

void print_number(int number) {
    std::cout << number << '\n';
}

int main() {
    // 用 std::function 创建一个函数对象，这个对象可以调用任何可以接受一个 int 参数的函数
    std::function<void(int)> func = print_number;
    
    // 我们可以通过 func 调用 print_number
    func(10); // prints 10
    
    // 我们也可以将 func 重新绑定到一个 lambda 表达式
    func = [](int number) { std::cout << 2 * number << '\n'; };
    
    // 现在 func 调用的是这个 lambda 表达式
    func(10); // prints 20
    
    
    
    
    // 函数指针的例子
    // typedef void (*FunctionPointer)(int);
    using FunctionPointer = void (*)(int);
    FunctionPointer function_pointer = print_number;
    // 使用函数指针调用函数
    function_pointer(10); // prints 10
    
    
    
    
    // 将函数指针绑定到 std::function 对象
    func = function_pointer;
    // 通过 std::function 对象调用函数
    func(10); // prints 10
    
    
    
    
    return 0;
}
```

在这个例子中，`std::function<void(int)>`声明了一个函数对象，这个对象可以调用任何接受一个int参数的函数。我们首先将其绑定到`print_number`函数，然后将其重新绑定到一个lambda表达式。无论绑定的是什么，我们都可以通过同样的方式调用`func`。

`std::function`特别有用，因为它可以用来存储任何可以调用的对象，不论这个对象的具体类型是什么。这使得我们可以在不知道函数具体类型的情况下使用函数，函数指针也好，函数对象也好，lambda表达式也好，我都能用。

例如在sort中，我们第三个参数可以传个lambda表达式来进行比较的判断。



### using和function

使用`using`定义函数指针和使用`std::function`调用函数之间有以下区别：

1. 类型声明方式不同：使用`using`定义函数指针时，需要显式指定函数指针的类型，例如 `using FunctionPtr = void (*)(int);`。而使用`std::function`时，可以直接使用函数的签名作为模板参数，例如 `std::function<void(int)>`。
2. 灵活性和多态性：`std::function`提供了更高的灵活性和多态性。它可以用于存储任何可调用对象，包括函数指针、函数对象、Lambda表达式等。这使得可以将不同类型的可调用对象赋值给同一个`std::function`对象，并且可以在运行时动态地改变所引用的函数或可调用对象。
3. 内存开销：由于`std::function`是一种通用的、多态的可调用对象的封装，它通常会引入一定的内存开销，包括存储可调用对象的函数指针或函数对象、存储可能的捕获变量等。而直接使用函数指针则没有额外的内存开销。
4. 性能：由于`std::function`的灵活性和多态性，它可能会引入一些额外的性能开销，例如函数指针的间接调用和虚函数的调用。而直接使用函数指针可以避免这些开销，从而在某些情况下具有更好的性能。

因此，选择使用`using`定义函数指针还是使用`std::function`调用函数取决于具体的需求和上下文。如果需要更高的灵活性、多态性和对不同类型的可调用对象的统一处理，可以使用`std::function`。如果关注性能和内存开销，并且只需要处理特定类型的函数指针，那么使用`using`定义函数指针可能更适合。





## 杂想

bind的有点像UE委托中的CreateLambda，因为最后都是形成了一个新的可调用对象，虽然UE委托中形成的是一个委托实例，这里形成的是一个函数对象

function有点像UE委托中的BindLambda，都是绑定到一个容器中的感觉，虽然UE委托中是绑定到一个委托上，这里是绑定到创建的特定function里

bind是将可调用函数与其参数绑定在一起形成新的可调用函数，function可以持有任何可调用对象