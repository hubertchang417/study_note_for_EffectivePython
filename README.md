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
## \_\_dict\_\_
## \_\_getattr\_\_
## \_\_getattribute\_\_
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
