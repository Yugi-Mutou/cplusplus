# RVO

RVO和NRVO都是C++中的优化技术，用于在函数返回时避免对象的拷贝和构造开销。

RVO是**Return Value Optimization**的缩写，即返回值优化。它是一种编译器优化技术，用于在函数返回时避免不必要的对象拷贝和构造开销。**在RVO优化中，编译器会将要返回的对象直接放在函数调用位置，从而避免了对象的拷贝和构造开销**。例如：

```c++
class MyClass {
public:
    MyClass(int value) : m_value(value) {}
    int getValue() const { return m_value; }
private:
    int m_value;
};

MyClass createMyClass(int value) {
    return MyClass(value);
}

int main() {
    MyClass obj = createMyClass(10);
    std::cout << obj.getValue() << std::endl;
    return 0;
}
```

在这个示例中，`createMyClass`函数直接返回一个`MyClass`对象，由于编译器进行了RVO优化，**不会调用复制构造函数**，直接将创建的对象作为返回值返回。





# NRVO

NRVO是Named Return Value Optimization的缩写，即命名返回值优化。它是一种编译器优化技术，与RVO类似，用于在函数返回时避免对象的拷贝和构造开销。在NRVO优化中，编译器会将要返回的对象直接放在**函数栈帧中的指定位置**，从而避免了对象的拷贝和构造开销。例如：

```c++
class MyClass {
public:
    MyClass(int value) : m_value(value) {}
    int getValue() const { return m_value; }
private:
    int m_value;
};

MyClass createMyClass(int value) {
    MyClass obj(value);
    return obj;
}

int main() {
    MyClass obj = createMyClass(10);
    std::cout << obj.getValue() << std::endl;
    return 0;
}
```

在这个示例中，`createMyClass`函数创建一个`MyClass`对象，并将其作为返回值返回。由于编译器进行了NRVO优化，**不会调用复制构造函数**，直接将创建的对象作为返回值返回。

总之，RVO和NRVO都是C++中的优化技术，用于在函数返回时避免对象的拷贝和构造开销。RVO优化是将要返回的对象直接放在函数调用位置，而NRVO优化是将要返回的对象直接放在函数栈帧中的指定位置。



# RVO和NRVO原理解释

在RVO和NRVO优化中，编译器会将函数返回值直接放在**调用者的栈空间中**或**函数栈帧中的指定位置**，从而避免了额外的对象拷贝和构造操作。

因此，不需要再将返回值复制给接收对象。接收对象会直接引用函数返回值的地址，从而实现对返回值的访问。