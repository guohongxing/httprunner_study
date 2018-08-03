#python用法
###特性property

    import math
    class Circle:
        def __init__(self,radius): #圆的半径radius
            self.radius=radius

        @property
        def area(self):
            return math.pi * self.radius**2 #计算面积

        @property
        def perimeter(self):
            return 2*math.pi*self.radius #计算周长

    c=Circle(10)
    print(c.radius)
    print(c.area) #可以向访问数据属性一样去访问area,会触发一个函数的执行,动态计算出一个值
    print(c.perimeter) #同上
    '''
    输出结果:
    314.1592653589793
    62.83185307179586
    '''
作用：

将一个类的函数定义成特性以后，对象再去使用的时候obj.name,根本无法察觉自己的name是执行了一个函数然后计算出来的，这种特性的使用方式遵循了统一访问的原则

2.静态方法：staticmethod
通常情况下，在类中定义的所有函数（跟self没有关系），是类本身的函数

    class Foo:
    def spam(x,y,z): #类中的一个函数，千万不要懵逼，self和x啥的没有不同都是参数名
        print(x,y,z)
    spam=staticmethod(spam) #把spam函数做成静态方法
    print(type(Foo.spam)) #类型本质就是函数
    Foo.spam(1,2,3) #调用函数应该有几个参数就传几个参数

    f1=Foo()
    f1.spam(3,3,3) #实例也可以使用,但通常静态方法都是给类用的,实例在使用时丧失了自动传值的机制

    '''
    <class 'function'>
    3
    3
    '''
###classmethod
类方法是给类用的，类在使用时会将类本身当作参数传给类方法的第一个参数，python为我们内置了函数classmethod 来把类中的函数定义为类方法

      class A:
    x=1
    @classmethod
    def test(cls):
        print(cls,cls.x)

    class B(A):
    x=2
    B.test()
    
    '''
      输出结果:
     <class '__main__.B'> 2
     '''
###  __str__的用法
     
     #__str__定义在类内部，必须返回一个字符串类型，
什么时候会出发它的执行呢？打印由这个类产生的对象时，会触发执行

    class People:
    def __init__(self,name,age):
        self.name=name
        self.age=age
    def __str__(self):
        return '<name:%s,age:%s>' %(self.name,self.age)

    p1=People('egon',18)
    print(p1)
    str(p1) #----->p1.__str__()


#模块
###模块的导入
模块主需要导入一次，不需要重复导入，多次导入和导入一次的效果是一致的，如果模块修改的话，可以使用reload(模块)2.7.3 ，目前3.0版本已经不支持，支持exec()重复导入
###模块的定义
1. import导入被测模块，为了区分模块是作为程序运行还是导入到其他程序，需要引入‘\_\_name__’变量;
  
  在主程序中：\__name__ == '\_\_main\_\_'
  
  在导入的模块中：\__name__ == 模块的名字

2.模块包的导入，统一存储在site-package包里面，可以通过系统目录sys.path查看，类似这样，可以把包加入site-package包里面

3.模块的探究：模块的真正里面的函数可以使用 dir(模块名)列出，可以使用\_\_all__列出通用的模块，如果模块本身没有设定，默认from 模块名 import *,导入的是不带\_\_的所有函数

##函数式编程

> func1 = lambda x :<expressiion()>
>
> 等同于：
>  def func1():
>     <expression()>
 
 