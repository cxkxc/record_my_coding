# debug

[TOC]

### 【Debug】— C++ 表达式必须包含类类型

#### 情况 1

```c++
class Test{
public:
    Test(){ }
    void foo(){ }
};

int main(int, char**){
    Test t();                // 编译器会将 t 视为一个函数；
    t.foo();                 // 出错，表达式必须包含类类型
    return  0;
}
```

修改方法：

```c++
// 对象的定义，修改为：
Test t;
```

当构造函数中存在一些参数时：

```c++
class Test{
public:
    Test(int i) {}                    // 但要避免默认单参构造函数：Test(int i = 0) {}
    ...
}

int main(){
    Test t(5);
    ...
}
```

#### 情况 2

```c++
Test* t = new Test(5);
// 错误访问成员函数的形式：
//t.foo();               
// 正确写法：
t->foo();           
```



### 【Debug】尝试对 unordered_map 按 key 值分类

This is impossible from both a compilation and logical standpoint. From a type standpoint, std::sort requires:

从编译和逻辑的角度来看,这是不可能的.从类型的角度来看,std::sort要求：

> -RandomIt must meet the requirements of ValueSwappable and RandomAccessIterator.
> -The type of dereferenced RandomIt must meet the requirements of MoveAssignable and MoveConstructible.

The iterator type on std::unordered_map is a ForwardIterator, not a RandomAccessIterator, so the first requirement is unsatisfied. The type of the dereferenced iterator is pair<const Key, T>, which is not MoveAssignable (can't assign to const), so the second requirement is also unsatisfied.

std :: unordered_map上的迭代器类型是ForwardIterator,而不是RandomAccessIterator,因此第一个要求不满足. 非关联的迭代器的类型是对< const Key,T>,它不是MoveAssignable(不能分配给const),因此第二个要求也不满足.

From a logical standpoint, sorting an unordered container makes no sense. It's unordered. And the complexity guarantees that unordered_map is able to achieve require a very specific ordering that you shouldn't be, and aren't, allowed to mess with.

从逻辑的角度来看，对无序容器进行排序毫无意义。这是无序的。这种复杂性保证了unordered_map能够实现，需要一种非常特定的顺序，而您不应该也不允许打乱这种顺序。

If you want to "sort" your unordered_map, put them in a vector:

```c++
std::vector<std::pair<int, int>> elems(table.begin(), table.end());
std::sort(elems.begin(), elems.end(), comp);
```



