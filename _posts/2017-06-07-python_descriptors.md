---
layout: post
title: Python 中的描述符
subtitle:
date:       2017-06-07
author:     "Scott"
header-img:
catalog:    false
tags:
    - Python
---

**注：本文是个人阅读 《Fluent Python》 第 6 部分和 观看 [Luciano Ramalho - Decorators and descriptors decoded - PyCon 2017](https://www.youtube.com/watch?v=81S01c9zytE&t=3502s) 的笔记**

假设有个销售散装有机食物的电商应用，客户可以按重量订购坚果、干果或杂粮，在这个系统中，每个订单中都有一系列商品，而每个商品都可以用下面类表示。



### LineItem 01 版


```python
>>> class LineItem(object):
...
...     def __init__(self, description, weight, price):
...         self.description = description
...         self.weight = weight
...         self.price = price
...         
...     def subtotal(self):
...         return self.weight * self.price

```

假设现在我们要买 10 斤黄葡萄干

```python
>>> raisins = LineItem('Golden raisins', 10, 6.95)
>>> raisins.subtotal()
69.5
>>> raisins.weight = -20
>>> raisins.subtotal()
-139.0
```

这居然可以把重量设为负数，显然不合理的。那如何解决呢？使用读值和设值管理属性自然是可以实现，如 [使用@property](http://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/00143186781871161bc8d6497004764b398401a401d4cce000) 这里前半部分的演示，而比较符合 Python 风格的做法是把数据属性换成特性。


### LineItem 02 版

```python
>>> class LineItem(object):
...
...     def __init__(self, description, weight, price):
...         self.description = description
...         self.weight = weight
...         self.price = price
...
...     def subtotal(self):
...         return self.weight * self.price
...
...     @property
...     def weight(self):
...         return self.__weight
...
...     @weight.setter
...     def weight(self, value):
...         if value > 0:
...             self.__weight = value
...         else:
...             raise ValueError('value must be > 0')
...
```

```python
>>> walnuts = LineItem('walnuts', -1, 10.00)
Traceback (most recent call last)
...
ValueError: value must be > 0
```

这样的确没办法设置 `weight` 为负数了，但如果需要把 `price` 也达到同样的效果，是否需要重写一遍特性呢？的确可以，但冗长的代码一点也不 Pythonic，这个时候就需要引入描述符(Descriptor)了。



### LineItem 03 版



```python
>>> class Quantity(object):
...
...     def __init__(self, storage_name):
...         self.storage_name = storage_name  
...
...     def __set__(self, instance, value):  
...         if value > 0:
...             instance.__dict__[self.storage_name] = value  
...         else:
...             raise ValueError('value must be > 0')
...
...
... class LineItem(object):
...     weight = Quantity('weight')  # <1>
...     price = Quantity('price')  # <2>
...
...     def __init__(self, description, weight, price):  
...         self.description = description
...         self.weight = weight
...         self.price = price
...
...     def subtotal(self):
...         return self.weight * self.price
```



```python
>>> truffle = LineItem('White truffle', 100, 0)
Traceback (most recent call last)
...
ValueError: value must be > 0
```

上面可以清晰的看到，是可以正常运作的，但仔细观察会发现，<1>/<2> 左右两边的 `price` / `weight` 都是重复的，这在复制粘贴的时候很容易出错，有没办法解决呢？当然。下面是 04 版


### LineItem 04 版

```python
>>> class Quantity(object):
...     instance_counter = 0
...
...     def __init__(self):
...         cls = self.__class__
...         prefix = cls.__name__.lower()
...         self.attr_name = '_%s_%s' % (prefix, cls.instance_counter)
...         cls.instance_counter += 1
...
...     def __get__(self, instance, owner):
...         return getattr(instance, self.attr_name)
...
...     def __set__(self, instance, value):
...         if value > 0:
...             setattr(instance, self.attr_name, value)
...         else:
...             raise ValueError('value must be > 0')
...
...
... class LineItem(object):
...     weight = Quantity()
...     price = Quantity()
...
...     def __init__(self, description, weight, price):
...         self.description = description
...         self.weight = weight
...         self.price = price
...
...     def subtotal(self):
...         return self.weight * self.price
```



```python
>>> coconuts = LineItem('Brazilian coconut', 20, 17.95)
```

因为托管属性的名称( `price` / `weight`) 与储存属性的名称( `_quantity_0` / `_quantity_1` )是不一样的，而且读者需做特殊处理，所以实现 `__get__` 方法时必要的，若是没有返回的就也是描述符自身了。

```python
>>> coconuts = LineItem('Brazilian coconut', 20, 17.95)
>>> coconuts.price
17.95
>>> coconuts.weight = 0
Traceback (most recent call last)
...
ValueError: value must be > 0
```

现在不需要把托管属性的名称传给 `Quantity` 构造方法了，但有个问题，储存属性是类名+数字，这样难以调试，最好是能把名称显示的提示出来

```python
>>> coconuts.__dict__
{'_quantity_0': 20, '_quantity_1': 17.95, 'description': 'Brazilian coconut'}
```


### LineItem 05 版


```python
>>> class Quantity(object):
...
...     def __get__(self, instance, owner):
...         return getattr(instance, self.target_name)  # <1>
...
...     def __set__(self, instance, value):
...         if not hasattr(self, 'target_name'):
...             self.set_target_names(instance)
...         if value > 0:
...             setattr(instance, self.target_name, value)
...         else:
...             raise ValueError('value must be > 0')
...
...     def set_target_names(self, instance, owner=None):
...         owner = owner if owner else instance.__class__
...         for key, attr in owner.__dict__.items():
...             # 对含有 set_target_names 方法或属性的类
...             # 设定 owner.__name__ + key 为 target_name 属性的值
...             if hasattr(attr, 'set_target_names'):
...                 setattr(attr, 'target_name', '_%s__%s' % (owner.__name__, key))
...
...                 
... class LineItem(object):
...     weight = Quantity()
...     price = Quantity()
...
...     def __init__(self, description, weight, price):
...         self.description = description
...         self.weight = weight
...         self.price = price
...
...     def subtotal(self):
...         return self.weight * self.price
...
```


```python
>>> raisins = LineItem('Golden raisins', 5, 2.48)
... raisins.description, raisins.weight, raisins.price
...
('Golden raisins', 5, 2.48)
>>> raisins.weight
5
>>> raisins.__dict__
{'_LineItem__price': 2.48,
 '_LineItem__weight': 5,
 'description': 'Golden raisins'}
>>> raisins.weight = 0
Traceback (most recent call last)
...
ValueError: value must be > 0
```

这一版该有的功能基本都有了，也方便调试，但 <1> 这个位置还是有个问题，若是没有创建实例，会报 `no attribute 'self.target_name'` 的错，因为 `set_target_name` 方法需要有实例后才运作，同时代码显得略臃肿。



### LineItem 06 版


```python
>>> class Quantity:
...
...     def __set__(self, instance, value):
...         if value <= 0:
...             raise ValueError('%r must be > 0' % self.storage_name)
...         # 若满足条件，则把托管实例属性的值设为 value
...         instance.__dict__[self.storage_name] = value
...
...         
... def named_fields(cls):
...     for name, attr in cls.__dict__.items():
...         if isinstance(attr, Quantity):
...             # 给描述符实例设定一个属性 storage_name，值为 name
...             attr.storage_name = name  
...     return cls
...
...
... @named_fields
... class LineItem(object):
...     weight = Quantity()
...     price = Quantity()
...
...     def __init__(self, description, weight, price):
...         self.description = description
...         self.weight = weight
...         self.price = price
...
...     def subtotal(self):
...         return self.weight * self.price
...
```



```python
>>> raisins = LineItem('Golden raisins', 5, 2.48)
>>> raisins.description, raisins.weight, raisins.price
('Golden raisins', 5, 2.48)
>>> raisins.__dict__
{'description': 'Golden raisins', 'price': 2.48, 'weight': 5}
>>> raisins.weight = 0
Traceback (most recent call last)
...
ValueError: 'weight' must be > 0
>>> raisins.price = -1
Traceback (most recent call last)
...
ValueError: 'price' must be > 0
```

运行正确、报错清晰、漂亮，所以这是很完美的解决方案，而且扩展性很强，比如若是我们对 `description` 也要做判定，只需加一个描述类，并在 named_field 里加一个判定就行


```python
... class Quantity:
...
...     def __set__(self, instance, value):
...         if value <= 0:
...             raise ValueError('%r must be > 0' % self.storage_name)
...         instance.__dict__[self.storage_name] = value
...
...         
... def named_fields(cls):
...     for name, attr in cls.__dict__.items():
...         if isinstance(attr, (NonBlank, Quantity)):
...             attr.storage_name = name
...     return cls
...
...
... @named_fields
... class LineItem(object):
...     description = NonBlank()
...     weight = Quantity()
...     price = Quantity()
...
...     def __init__(self, description, weight, price):
...         self.description = description
...         self.weight = weight
...         self.price = price
...
...     def subtotal(self):
...         return self.weight * self.price
...
```

运行结果，有两种判定

```python
>>> raisins = LineItem('', 5, 2.48)
Traceback (most recent call last)
...
ValueError: 'description' must not be empty
>>> raisins = LineItem('Golden raisins', 0, 2.48)
Traceback (most recent call last)
...
ValueError: 'weight' must be > 0

```
