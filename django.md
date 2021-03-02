# Django

django是一个app，需要在服务器的基础上才能运行

里边实际上就是很多函数的调用

## djangoORM

对象关系映射：将数据库里边的表一一映射为python中的类型

~~~python

import os
import django
os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'first.settings')
django.setup(set_prefix=False)
#########################################这四行代码能在本地进行测试
from usr.models import Employee
# print(*Employee.__dict__.items(), sep='\n')
mgr = Employee.objects  # 管理对象，一个表里至少有一个管理者

# 四个返回查询集的方法
x = mgr.all()  # 返回的是查询集， 时惰性对象，如果不驱动他则不会查询，啥也没有。惰性：不立即占用内存，
for i in mgr.filter(emp_no=10001).values('pk'):  # 相当于where, exclude()相当于where not。反取，不好，反取数据量可能很大
    print(i)
## 关键字传参，其中可以用pk指代主键

# for i in mgr.values():  # 投影，啥也不i写相当于*
#     print(i)

# orderby 排序，一般放在后边,接收参数为字符串
for i in mgr.exclude(emp_no=10010).values('pk', 'gender').order_by('-pk', 'gender'):
    print(i)
~~~



