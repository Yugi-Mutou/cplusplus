# move

c++primer 611页

```c++
template <typename T>
typename remove_reference<T>::type&& move(T&& t) {
    return static_cast<typename remove_reference<T>::type&&>(t);
}
```



# forward

```c++
template <class T>
typename remove_reference<T>::type&& forward(typename tinySTL::remove_reference<T>::type& t) noexcept {
    return static_cast<T&&>(t);
}

template <class T>
typename remove_reference<T>::type&& forward(typename tinySTL::remove_reference<T>::type&& t) noexcept {
    return static_cast<T&&>(t);
}
```

当`t`是左值引用时，此时推到得到`T = X&`，则`T&&`展开为`X& &&`，经过引用折叠后得到`X&`，即最后返回`static_cast<X&>(t)`。

当`t`是右值引用时，此时推到得到`T = X&&`，则`T&&`展开为`X&& &&`，经过引用折叠后得到`X&&`，即最后返回`static_cast<X&&>(t)`。





# remove_reference

```c++
template <class T>
struct remove_reference {
    using type = T;
};

// 特化版本
template <class T>
struct remove_reference<T&> {
    using type = T;
};

template <class T>
struct remove_reference<T&&> {
    using type = T;
};
```

