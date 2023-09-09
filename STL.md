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

### 释放内存

#### 一级配置器

```c++
static void deallocate(void* p, size_t /* n */){
    free(p);
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



# vector

vector类将其元素保存在连续内存中。为了获得可接受的性能，vector预先分配足够的内存来保存可能需要的更多元素。vector的每个添加元素的成员函数会检查是否有空间容纳更多的元素。

- 如果有，成员函数会在下一个可用位置构造一个对象
- 如果没有，vector会重新分配空间，它获得新的空间，并将已有元素移到新空间中，释放旧空间，并添加新元素。