# study note - Effective Python

## Mutable object
物件的數值是可以在創造之後做改變，在Python中，list、set與dict是可以在宣告之後做改變。

## Immutable object
物件的數值是可以在創造之後無法做改變，在Python中，int、float、bool、str、tuple與unicode無法在創造之後做改變。
```Python
foo = 1 # foo -> int(1)
foo = 2 # foo -> int(2)
```
例如以上例子，foo在將值assign為 2 時，是另外創造另一個物件值為 2，在將他指向給 foo
| Class | Description                        | Immutable |
|-------|:----------------------------------:|----------:|
| bool  |  Boolean value                     |   Yes     |
| int   |  interger                          |   Yes     |
| float |  floating-point number             |   Yes     |
| list  |  mutable sequence of objects       |   No      |
| tuple |  immutable sequence of objects     |   Yes     |
| str   |  string                            |   Yes     |
| set   |  unordered set of distinct objects |   No      |
| dict  |  dictionary                        |   No      |

(https://medium.com/@meghamohan/mutable-and-immutable-side-of-python-c2145cf72747)

## First-class Function
First class function 特性:
* A function is an instance of the Object type.
* You can store the function in a variable.
* You can pass the function as a parameter to another function.
* You can return the function from a function.
* You can store them in data structures such as hash tables, lists etc.

(https://stackoverflow.com/questions/27392402/what-is-first-class-function-in-python)

# Sepcial class method
## \_\_init\_\_
當創造一個 class 物件時，進行初始化。
## \_\_call\_\_
Python可以允許class建立 __call__ method，讓class物件可以像function一樣被呼叫。(Item 38)
```python
class Test:
    def __init__(self):
      self.x = 99
      self.y = 88
     
    def __call__(self):
        print(f'Before x:{self.x}, y:{self.y}')
        self.x -= 1
        self.y -= 1
        print(f'After  x:{self.x}, y:{self.y}')
 
if __name__ == '__main__':
    a = Test()
    print(f'Test is callable:',callable(a))
    a()
    # > Test is callable: True
    # > Before x:99, y:88
    # > After  x:98, y:87
```
## \_\_repr\_\_ & \_\_str\_\_
```python
class Test:
    def __init__(self):
        self.msg = 'hello'
   
    def __str__(self):
        return f'I am str'
   
    def __repr__(self):
        return f'I am repr'
 
class Blank:
    def __init__(self):
        pass
if __name__ == '__main__':
    a = Test()
    # print 會調用直接 __str__
    print(a)
    print(repr(a))
    
    b = Blank()
    print(b)
    
    # > I am str
    # > I am repr
    # > <__main__.Blank object at 0x000001F42091BBE0>
```
## \_\_iter\_\_
在python中， ```for x in foo``` 事實上就是呼叫```iter(foo)```來執行迴圈， 
而在class物件中，可以設定 \_\_iter\_\_ method 讓我們的class像一個generator來執行
```Python
class Test:
    def __init__(self, max_size):
        self.max_size = max_size
   
    def __iter__(self):
        for i in range(self.max_size):
            yield int(i)
           
           
if __name__ == '__main__':
   
    a = Test(10)
    print(list(a))
    # > [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
```
也可以在 ( )之中放入list comprehebsion 來創造generator (item 32)
```Python
it = (len(x) for x in range(10))
print(it)
print(next(it))
print(next(it))
# > <generator object <genexpr> at 0x0000023A853A2110>
# > 0
# > 1
```
## \_\_dict\_\_
查看物件內的參數，以dictionary的形式儲存
```Python
class Test:
    def __init__(self):
        self.apple = 'I am apple'
        self.orange = 'I am orange'  
 
if __name__ == '__main__':
    a = Test()
    print(Test.__dict__)
    print(a.__dict__)
    # > {'__module__': '__main__', '__init__': <function Test.__init__ at 0x0000021463A6A0E0>, '__dict__': <attribute '__dict__' of 'Test' objects>, '__weakref__': <attribute '__weakref__' of 'Test' objects>, '__doc__': None}
    # > {'apple': 'I am apple', 'orange': 'I am orange'}
```
## descriptor
因為 @property 無法 reuse 的問題，我們可以使用descriptor來解決問題(item 46)，依照descriptor protocol 我們可以定義如何存取attribute
```python
class Test:
    def __init__(self):
        self.foo = 100
   
    @staticmethod
    def _check_value(attr,value):
        print(f'my {attr} is {value} in Test')
   
    @property  
    def foo(self):
        return self._foo
   
    @foo.setter
    def foo( self, value):
        self._check_value('foo', value)
        self._foo = value
           
class MyValue:
    def __init__(self):
        self._value = 0
       
    def __get__(self, instance, instance_type):
        return self._value
   
    def __set__(self, instance, value):
        print(f'my {dir(instance)[-1]} is {value} in {instance.__class__.__name__}')
        self._value = value
 
class Test1:    
    foo = MyValue()
 
if __name__ == '__main__':
   
    a = Test()
    b = Test1()
    b.foo = 100
    # > my foo is 100 in Test
    # > my foo is 100 in Test1
```
## getattr(object, name[, default])
Python built-in function 取得物件的屬性
```python
class Test:
    def __init__(self):
        self.foo = 100
       
if __name__ == '__main__':
    a = Test()
    print(getattr(a, 'foo')) # exist
    print(getattr(a, 'bar', -1)) #if not exist output -1
    # > 100
    # > -1
```
## \_\_getattr\_\_
當我們在呼叫物件內未存在的屬性時，該物件會呼叫 \_\_getattr\_\_ method
```python
class Test:
    def __init__(self):
        self.foo = 100
       
    def __getattr__(self, name):
        print(f'not found {name} in the object')
       
if __name__ == '__main__':
    a = Test()
    print(a.foo) # exist
    print(a.bar) #not exist
    # > 100
    # > not found bar in the object
    # > None
```
## \_\_getattribute\_\_
\_\_getattribute\_\_ 與 \_\_getattr\_\_ 方法相似，但若class物件既有的attribute不會透過\_\_getattr\_\_呼叫，而\_\_getattribute\_\_不管是不是既有只要提取屬性就會呼叫\_\_getattribute\_\_(item 47)
```python
class Test:
    def __init__(self):
        self.exists = 100
   
    def __getattr__(self, name):
        value = f'Value for {name}'
        setattr(self, name, value)
        return value
   
class LoggingTest(Test):
    def __getattr__(self, name):
        print(f'* Called __getattr__({name!r}), '
              f'populating instance dictionary')
        result = super().__getattr__(name)
        print(f'* Returning {result!r}')
        return result
   
 
class Test1:    
    def __init__(self):
        self.exists = 100
       
    def __getattribute__(self, name):
        print(f'* Called __getattribute__({name!r})')
        try:
            value = super().__getattribute__(name)
            print(f'* Found {name!r}, return {value!r}')
            return value
        except AttributeError:
            value = f'Value for {name}'
            print(f'* Setting {name!r} to {value!r}')
            setattr(self, name, value)
            return value
 
if __name__ == '__main__':
    print('---- With __getattr__ ----')
    a = LoggingTest()
    print('exists:     ', a.exists)
    print('First foo:  ', a.foo)
    print('Second foo: ', a.foo)
    print('---- With __getattribute__ ----')
    b = Test1()
    print('exists:     ', b.exists)
    print('First foo:  ', b.foo)
    print('Second foo: ', b.foo)
    # > ---- With __getattr__ ----
    # > exists:      100
    # > * Called __getattr__('foo'), populating instance dictionary
    # > * Returning 'Value for foo'
    # > First foo:   Value for foo
    # > Second foo:  Value for foo
    # > ---- With __getattribute__ ----
    # > * Called __getattribute__('exists')
    # > * Found 'exists', return 100
    # > exists:      100
    # > * Called __getattribute__('foo')
    # > * Setting 'foo' to 'Value for foo'
    # > First foo:   Value for foo
    # > * Called __getattribute__('foo')
    # > * Found 'foo', return 'Value for foo'
    # > Second foo:  Value for foo

```
## hasattr(object, name)
判斷物件是否有指定屬性，hasattr在確認屬性是否存在前，是會使用\_\_getattr\_\_確認屬性的存在(Item 47)。

## Decorator
```python
def log(func):
    def warp(*args, **kwargs):
       
        for i, val in enumerate(args, 1):
            print(f'my arg{i}: {val}')
           
        for key, val in kwargs.items():
            print(f'my kwargs {key}: {val}')
        func(*args, **kwargs)
    return warp
 
 
@log
def hey(a, b, k1=9, k2=11):
    print('hello!')
    print(k1)
    print(k2)
   
if __name__ == '__main__':
    hey(1,2,k1=10)
    
    # > my arg1: 1
    # > my arg2: 2
    # > my kwargs k1: 10
    # > hello!
    # > 10
    # > 11
```
## yield from
我們可以透過 yield from 來組合多個Generators(Item 33)
```Python
def move(period, speed):
    for _ in range(period):
        yield speed
 
def pause(delay):
    for _ in range(delay):
        yield 0
def animate():
    for delta in move(2, 5.0):
        yield delta
    for delta in pause(1):
        yield delta
                   
def new_animate():
    yield  from move(2, 5.0)
    yield  from pause(1)
     
def run(func):
    print(func.__name__)
    for delta in func():
        print(delta)    
 
if __name__ == '__main__':
    run(animate)
    run(new_animate)
    # > animate
    # > 5.0
    # > 5.0
    # > 0
    # > new_animate
    # > 5.0
    # > 5.0
    # > 0

```
## next()
當我們建立一個generator，我們可以使用next()來呼叫generator內的元素，但當我們把generator內的元素都呼叫完時，就無法再從頭呼叫，而我們可以一個container class，並且它可以執行iterator protocol(在Python中，很多像for sum()等語法是呼叫物件的\_\_iter\_\_ method來運行)，當我們在使用這些container class時，就可以避免generator exhausted的問題
```python
class Test:
    def __init__(self, size):
        self.size = size
   
    def __iter__(self):
        for i in range(self.size):
            yield i
           
           
def another_Test(size):
    for i in range(size):
        yield i
if __name__ == '__main__':
    a = another_Test(10)
    print(list(a))
    print(list(a))
    # > [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
    # > []
   
    b = Test(10)
    print(list(b))
    print(list(b))
    # > [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
    # > [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
 

from collections.abc import Iterable, Iterator
class Test:
    def __init__(self, size):
        self.size = size
   
    def get_item(self,start=0):
        chk = 0
        for i in range(start, self.size):
            yield i
            print(f'inside {i}, chk {chk}')
            chk+=1
       
    def __iter__(self):
        return self.get_item()
if __name__ == '__main__':
   
    b = Test(10)
   
    print(f'my container Iterable:{isinstance(b, Iterable)}, Iterator:{isinstance(b, Iterator)}')
    for i in b:
        print(f'outside: {i}')
    # > my container Iterable:True, Iterator:False
    # > outside: 0
    # > inside 0, chk 0
    # > outside: 1
    # > inside 1, chk 1
    # > outside: 2
    # > inside 2, chk 2
    # > outside: 3
    # > inside 3, chk 3
    # > outside: 4
    # > inside 4, chk 4
    # > outside: 5
    # > inside 5, chk 5
    # > outside: 6
    # > inside 6, chk 6
    # > outside: 7
    # > inside 7, chk 7
    # > outside: 8
    # > inside 8, chk 8
    # > outside: 9
    # > inside 9, chk 9
 
 
 

from collections.abc import Iterable, Iterator
class Test:
    def __init__(self, size):
        self.size = size
        self.idx = 0
   
    def __next__(self):
        if self.idx < self.size:
            res = self.idx
            self.idx += 1
            return res
        else:
            raise StopIteration
       
       
    def __iter__(self):
        return self
           
if __name__ == '__main__':
   
    b = Test(10)
   
    print(f'my container Iterable:{isinstance(b, Iterable)}, Iterator:{isinstance(b, Iterator)}')
    for i in b:
        print(f'outside: {i}')
    print(list(b))
    print(list(b))
    # > my container Iterable:True, Iterator:True
    # > outside: 0
    # > outside: 1
    # > outside: 2
    # > outside: 3
    # > outside: 4
    # > outside: 5
    # > outside: 6
    # > outside: 7
    # > outside: 8
    # > outside: 9
    # > []
    # > []
```
## send()
## itertools
Item 36 範例

