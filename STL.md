# 空间配置器

## new描述

- "Placement new" 用于在已经分配的内存或指定的内存地址上构造对象
  - operator new()相当于malloc()，operator delete()相当于free()

```c++
// 分配内存
void* memory=operator new(sizeof(MyClass));
// 在已分配的内存上用placement new构造对象
MyClass* obj =new (memory) MyClass(42);
// 手动调用析构函数,销毁对象
obj->~MyClass();
// 释放内存
operator delete(memory);
```

- 普通的 new 操作符在堆上分配内存并构造对象

```c++
class Foo{};
// 调用operator new配置内存，调用Foo::Foo()构造对象
Foo* pf = new Foo();
// 调用Foo::~Foo()将对象析构，调用operator delete释放内存
delete pf;
```

- 重载new和delete

```c++
class MyClass {
public:
    void* operator new(size_t size, int extraParam) {
        // 自定义内存分配逻辑，可以使用额外参数
        void* ptr = malloc(size + extraParam);
        // ...
        return ptr;
    }

    void operator delete(void* ptr, int extraParam) {
        // 自定义内存释放逻辑，可以使用额外参数
        // ...
        free(ptr);
    }
};
```



### 构造

```c++
#include<new.h>
template<class T1, class T2>
inline void construct(T1* p, const T2& value){
    // 在已分配内存的p上调用类T1的构造函数，构造对象
    // placement new
    new(p) T1(value);
}
```

### 析构

#### 指针参数析构

```c++
#include<new.h>
template <class T>
inline void destroy<T* pointer>{
    pointer->~T();
}
```

### 配置内存

#### 一级配置器

```c++
static void* allocate(size_t n){
    // operator new()相当于malloc()
    void* result = malloc(n);
    // oom = out of memory
    if(result==0) result==omm_malloc(n);
    return result;
}
```

#### 次级配置器(内存池)

```c++
union obj{
	union obj* free_list_link;
    char client_data[1];
};
// 利用这个结构体，一个8bits的数据既可以当指针又可以当8bits大小的数据
```



### 释放内存

#### 一级配置器

```c++
static void deallocate(void* p, size_t /* n */){
    free(p);
}
```



# vector

vector类将其元素保存在连续内存中。为了获得可接受的性能，vector预先分配足够的内存来保存可能需要的更多元素。vector的每个添加元素的成员函数会检查是否有空间容纳更多的元素。

- 如果有，成员函数会在下一个可用位置构造一个对象
- 如果没有，vector会重新分配空间，它获得新的空间，并将已有元素移到新空间中，释放旧空间，并添加新元素。



## emplace和push

push_back是vector的一个**普通成员函数**，有2个重载，分别接受左值和右值。

emplace_back是vector的一个**模板成员函数**，没有重载，接受左值和右值。只有将emplace_back写成模板成员函数，它的参数才可以写成Args &&的形式，才可以激活万能引用，这样才既可以接受左值，又可以接受右值。

```c++
template<typename _Tp, typename _Alloc>
template<typename... _Args>
void vector<_Tp, _Alloc>::emplace_back(_Args&&... __args)
{
    if (this->_M_impl._M_finish != this->_M_impl._M_end_of_storage)
    {
        // _Alloc_traits::construct 是一个模板函数，用于在指定的内存位置上构造对象
        // this->_M_impl是一个用于存储数据的一个成员变量
        // this->_M_impl._M_finish 是一个指向 vector 中当前末尾元素的迭代器
        // std::forward<_Args>(__args)... 将 __args 参数以原始的值类别（左值或右值）转发给 construct 函数
        _Alloc_traits::construct(this->_M_impl, this->_M_impl._M_finish,std::forward<_Args>(__args)...);
        ++this->_M_impl._M_finish;
    }
    else
        _M_emplace_back_aux(std::forward<_Args>(__args)...);
}
```

`..` 中的三个点号是C++中的展开操作符（unpacking operator）。展开操作符的作用是将参数包（parameter pack）中的所有元素展开成独立的参数，以便传递给函数或模板。





STL源码剖析中的push_back

```c++
void push_back(const T& x){
    if(finish!=end_of_storage){ //还有备用空间
        construct(finish,x);
        ++finish;
    }else{
        insert_aux(end(),x);
    }
}
```







1、接受const T&、T&&作为参数时，push_back和emplace_back是一样的效果。如果我们查看下vector的源代码，会发现其实push_back内部也是调用的emplace_back。

2、接受A的其他版本的构造函数的参数时，只能使用emplace_back，并且非常高效，期间不会发生临时变量的生成和拷贝。