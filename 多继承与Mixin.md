#  多继承

## 多继承的弊端

多继承会带来二义性，两个父类中都有同名的方法该如何处理

多继承中应该消除不确定性。

多继承会引入复杂性，除非必要不建议使用。复杂=>难以解决

###  多继承形成图

- 图的遍历有深度优先和广度优先

  - 要解决二义性就应该明确查找顺序

  - py3中使用C3算法采用深度优先，解决了二义性问题将继承当中的图变成了顺序结构

    ~~~ python
    # 保持单调性
    class A(B, C):pass
    class D(A):pass 
    # 则继承列表__mro__中也应该是D,A,B,C
    ~~~

##  抽象基类

只要有一个方法中含有未实现异常raise NotImplementedError()则称该方法为抽象方法 类为抽象基类

其他编程语言不允许抽像类实例化。

#### 应用场景

基类知道将来的子类必须实现某一个方法，可以在基类中先定义该方法的抽象方法，给子类覆盖使用。

子类在定义时也应当覆盖所有父类中的抽象方法

## Mixin类

Mixin本质上是类的多继承实现的

可以 为类添加一些功能，体现的是一种组合的设计模式

多组合，少继承

####  应用场景

~~~ python
# 有一个doc类，需要为其子类word添加一个功能，如打印功能
# 实现1.装饰器 2.Mixin类
class Doc:
    def __init__(self):
        self.content = "content"
 
class Word(Doc):
    pass

class PrintableMixin:
    def print(self):
    	print("Mixin print {}".format(self.content))
    
class PrintWord(PrintableMixin, Word):
    pass
# 这里的mixin应该放前面插队，这样在方法接解析顺序列表中mro()mixin的方法能够被优先使用
# mixin相对于装饰器来说1能够继承，2不会改变类的属性如PrintWord
~~~

####  使用原则

- mixin类只是为了提供一个方法不应该具有实例化方法
- Mixin类通常不能独立工作，因为它是准备混入别的类中的部分功能实现
- Mixin类是类，也可以继承，其祖先类也应是Mixin类

将mixin当作父类继承时应该将其放在靠前位置



