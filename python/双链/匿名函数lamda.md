# 匿名函数lamda
函数名 = lambda 入参：参数的表达式
```python
func = lambda : 1 + 1
print(func)                             #内存地址
print(func())                           #2
func = lambda m, n: m if m > n else n   #只能用if三目运算/列表推导式 其他for，while和普通if都不行
print(func(11, 2))                      #11
```

## 内置函数map 根据提供的函数对指定序列做映射
map(function, iterable, ...)
第一个参数 function 以参数序列中的每一个元素调用 function 函数，返回包含每次 function 函数返回值的新列表。
Python 3.x 返回迭代器。

```python
result_list = map(lambda n: n ** 2, [1, 2, 3])
print(result_list)                             #返回迭代器
print(list(result_list))                       #[1,4,9]
```

## 内置函数reduce  函数会对参数序列中元素进行累积
reduce(function, iterable[, initializer])
函数将一个数据集合（链表，元组等）中的所有数据进行下列操作：用传给 reduce 中的函数 function（有两个参数）先对集合中的第 1、2 个元素进行操作，得到的结果再与第三个数据用 function 函数运算，最后得到一个结果。

```python
from functools import reduce  #必须
sum = reduce(lambda x, y: x+y, [1,2,3,4,5])  # 使用 lambda 匿名函数  
print(sum)  #15
```

## 内置函数filter  函数用于过滤序列
filter(function, iterable)
该接收两个参数，第一个为函数，第二个为序列，序列的每个元素作为参数传递给函数进行判断，然后返回 True 或 False，最后将返回 True 的元素放到新列表中。
返回的一样是迭代器

```python
#过滤出只是数字的
my_list = ['123','234','abc','@#$','  ','abc234','132abc']
num_list = filter(lambda s: s.isdigit(), my_list)  
print(list(num_list))   #['123', '234']
```

## 内置函数sorted 函数对所有可迭代的对象进行排序操作

^21e6bc

**sort 与 sorted 区别：**
sort 是应用在 list 上的方法，sorted 可以对所有可迭代的对象进行排序操作。
list 的 sort 方法返回的是对已经存在的列表进行操作，无返回值，而内建函数 sorted 方法返回的是一个新的 list，而不是在原来的基础上进行的操作。 ^9c260c

sorted(iterable, cmp=None, key=None, reverse=False) ^afe8f1
```python
my_list.sort(key=lambda d: d['age'],reverse=True)  
print(my_list)  
  
students = [('john', 'A', 15), ('jane', 'B', 12), ('dave', 'B', 10)]  
print(sorted(students, key=lambda s: s[2]))
```

^ebb177

