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

# for i in mgr.values():  # 投影，啥也不写相当于*
#     print(i)

# orderby 排序，一般放在后边,接收参数为字符串
for i in mgr.exclude(emp_no=10010).values('pk', 'gender').order_by('-pk', 'gender'):
    print(i)
~~~

~~~python
import os
import django
from django.db.models import Q
os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'first.settings')
django.setup(set_prefix=False)
#########################################这四行代码能在本地进行测试
from usr.models import Employee, Salary, Dept_emp, Departments, Titles
# print(*Employee.__dict__.items(), sep='\n')
mgr = Employee.objects  # 管理对象，一个表里至少有一个管理者

# 四个返回查询集的方法
# x = mgr.all()  # 返回的是查询集， 时惰性对象，如果不驱动他则不会查询，啥也没有。惰性：不立即占用内存，
# for i in mgr.filter(emp_no=10001).values('pk'):  # 相当于where, exclude()相当于where not。反取，不好，反取数据量可能很大
#     print(i)
## 关键字传参，其中可以用pk指代主键

# for i in mgr.values():  # 投影，啥也不i写相当于*
#     print(i)

# orderby 排序，一般放在后边,接收参数为字符串
# for i in mgr.exclude(emp_no=10010).values('pk', 'gender').order_by('-pk', 'gender'):
#     print(i)


# 字段查询
# x = mgr.filter(first_name__istartswith='P')  # 加i忽略大小写
# x = mgr.filter(last_name__startswith='P').count() # 以双下划线分割，所以字段名（类属性不能是双下划线）



# and
# x = mgr.filter(pk__gt=10005, pk__lt=10010)
# x = mgr.filter(pk__gt=10005) & mgr.filter(pk__lt=10010)
# 使用Q对象进行包装,更方便进行逻辑运算
# x = mgr.filter(Q(pk__gt=10001) & Q(pk__lt=10010))

# or
# x = mgr.filter(pk__lt=10003) | mgr.filter(pk__gt=10018)
# x = mgr.filter(Q(pk__lt=10003)|Q(pk__gt=10010))

# 聚合， 首先需要导入聚合函数，Q对象那里导入:
# aggregate()方法实现聚合，返回字典，相当于返回一行数据所以椅字典的形式
from django.db.models import Q, Max, Min, Sum, Count, Avg

# x = mgr.filter(pk__lte=10005).aggregate(Count('pk'), Max('pk'))  # 返回的是字典
# 不可以取别名
# x = mgr.filter(pk__lte=10005).aggregate(Count(c='pk'), Max('pk'))

# annotate()实现分组，返回查询集, 参数：聚合的字段， 前边加values()表明分组的字段
# 参数：聚合的字段.以关键字传参可以取别名
# 返回查询集，元素是字典
# x = mgr.filter(pk__gte=10005).values('gender').annotate(a=Avg('pk'), c=Count('pk'))

print(Employee.__dict__.keys())
print(Salary.__dict__.keys())
# 可以看见多了两个特殊属性
# Salary中多了emp_no_id, 这是真正指向Salary表的emp_no，而emp_no属性是一个关系，指向员工表中对应的实体
# 员工表中多了salary_set（可以改名的）， 这也是一个关系，一对多的关系，一个emp_no对应着多个工资表中的实体emp_no


smgr = Salary.objects
# x = smgr.all()
# 查询某个员工的所有工资
# 1.从员工表中查询

# x = mgr.filter(pk=10004)
# for e in x:
#     print(e.name, e.salary_set.all()) # 只是e.salary_set不会有结果，因为没有真正查询，需要驱动它

# 从工资表中查询，比较复杂了

# x = smgr.filter(emp_no_id=10004)  # # django会给外键字段自动加后缀_id，如果不需要加这个后缀，用db_column指定
# flag = False
# name = ''
# for i in x:
#     if not flag:
#         name = i.emp_no.name
#         print(name, i.salary)  # 每次打印都会查员工表
#         flag = True
#     print(name, i.salary)

# 查询10010员工的所在的部门编号及员工信息,多对多关系
# 先通过员工表查到第三表，再通过第三表查部门
# 员工表---第三表----部门表
#
# dmgr = Departments.objects
# demgr = Dept_emp.objects
#
# x = mgr.get(pk=10010)
# for i in x.eset.all():
#     print(i.dept_no)
#     print(i.emp_no.name)

# 查询10009员工所有的头衔
tmgr = Titles.objects

x = mgr.get(emp_no=10009)
flag = True
name = ''

for e in x.titles_set.all():
    if flag:
        name = e.emp_no.name
        flag = False
    print(name, e.title, e.from_date)

# print(type(x))
print(x)

~~~

## 路由层urls

路由层负责的是路由匹配，也就是根据资源定位符的后缀定位到相应的业务函数进行处理

~~~python
# django == 1.11.11
from app01 import views
# 首页的设置
url('^$', views.home)  # 没有任何后缀则进入首页 
url('^index/$', views.index)  # 按顺序进行正则表达式的匹配，成功则执行对应的views函数
url('^index/(\d+)/', views.index)  # 无名分组：将分组的内容（\d+）以位置参数的形式传递给试图函数。所以需要先设置形参接收位置参数，不然就报错。设置为可变位置参数就行
# 可以设值多个分组，对象多个形参
url('^index/(\w+)/(\d+)/', views.index)

# 有名分组, 在正则表达式中给分组起别名以关键字参数的形式传递
url('^index/(?P<year>\d+)', views.index) 


~~~

#### CBV与FBV

~~~python
# cbv利用元类编程

~~~





## 模板层

{{  }}：变量相关， 传入变量名

{% %}：逻辑相关  如for循环

~~~python
# 模板传值语法
# 可以将任何py的数据类型传到前端
# 如果是可调用对象就会调用他（不能传参数）////////////////
# 对于字典/列表的值的访问只能使用句点符.可以点索引，点键

~~~

### 过滤器

~~~python
# 基本语法
{{ name|filter:args}}
# filter是django已经定义好的函数。相当于调用filter(name, args)。最后显示出返回值
h5 = '<h1>ff<h1>'
# 重要 转义问题
{{ h5|safe }}
# 默认是不安全，前端会不会渲染/执行 后端传入的数据。
# 设置为safe前端就会执行js代码/渲染html语法
~~~

## 标签

逻辑相关

~~~python
# for循环
{% for i in d %}
<p>{{ forloop }}</p>  # 自带的记录循环装置的字典？
{% empty%}
空的可迭代对现象会执行该句
{% endfor %}

# if
{% if content %}
{%elif%}
{%else%}

{% endif %}
# with 取别名用
~~~

### 自定义过滤器，标签， inclusion_tag

~~~python
# 准备工作
# 1.在app模块下必须创建templatetags目录
# 2.在该目录下创建任意py文件
# 3.必须导入
from django import template

register = template.Library()

@register.filter(name='int')
def toint(valuer, arg):  # 最多接收两个参数
    pass

@register.simple_tag(name='add_')
def add_(*args):
    pass  # 可以接收多个参数。经过处理在返回到前端

# 自定义inclusion_tag
# 调用这个方法，该方法对应另一个html页面，该方法实际上是处理另一个页面，最后将这个页面返回到调用出，如侧边栏等多种场合都能用到的功能。将之独立出来
# 
@register.inclusion_tag('left_menu.html')  # 处理数据后返回给的html页面。最后将会在调用页面将这个页面渲染出去
def plug(xx):
    x = int(xx)
    x = ['the {}'.format(i) for i in range(xx)]
    retuen locals()  # 这里返回数据的方式和render-content参数一致
    # 将数据返回个left_menu.html，在left中处理好该数据就先行
    
    
# 在网页中使用自定义模板时需要先导入
{% load templatename %}
~~~

## 模板的继承****

~~~python
# 好用
# 子页面拥有父页面的全部内容，并且不要在此写<html>标签
# 子页面的写法只需要如下一句就能正常运行
{% extend 'home.html' %}  # 接父页面的名称就行了
# 此时子页面的内容和home一模一样
# 为了定制自己独有的内容，需要在父页面中给需要改变的区域加上
# {% block 'name' %} 给一块内容指定名字，用于在子页面中修改
# {% endblock %}

# 一般来说一个父页面中至少有三个区域
1. {% block 'css' %} 
	用于子页面设置自己的css样式
   {% endblock %}
2. {% block 'html' %} 
	用于子页面展示自己的html
   {% endblock %}
3。{% block 'js' %} 
	用于子页面设置自己的js代码
   {% endblock %}

~~~

## 模板的导入

~~~python
# 将一个组件（html页面）导入到其他页面上
# 语法
{% include 'template.html'%}
# 将模板组件导入到当前的html页面/


~~~



### AJAX相关Jquery

~~~python
'''
如何实时渲染图片
绑定一个文本域变化事件
有变化即触发事件
文件阅读器对象
1.先生成一个文件阅读器对象
2.获取用户上传的文件对象
3.将文件对象交给文件阅读器
4.最后将文件阅读器的结果返回个前端src属性实现修改图片
$('#myfile').change(function(){
	let myfilereaderobj = new FileReader();
	let file_obj = $(this)[0].file[0];
	// 生成文件对象， this指的是#myfile
	myfilereaderobj.readasDataURL(file_obj);
	//异步操作， 所以需要等待文件对象加载完毕后再渲染到img标签
	myfilereaderobj.onload = function(){
		$('#myimg').attr('src', myfilereaderobj.result);
		//修改img标签的src属性实现图片的更换
	}
})

'''
'''
ajax发送请求
$('#id_commit').click(function(){
	let formdataobj = new FormData();
	// 1. 添加普通的键值对,不包含文件对象。自己手动添加就行
	// 由于是由form组件渲染的input标签，不好直接获得该标签的name value
	// 因此只需要再form标签 添加id，由$('#myform').serializeArray()获取。
	// 数据格式 [{}, {}, {}]。里边就是自定义对象了
	// 遍历数组
	$.each($('#myform').serializeArray(), function(index, obj){
		// index为对象的索引
		// obj 为自定义对象如:{"name":"usrname": "value":"edenia" }
		formdataobj.append(obj.name, obj.value);
	})
	// 2.添加文件对象
	formdataobj.append('avatar', $('#myfile')[0].file[0])
	// 3. 发送ajax请求
	$.ajax({
		url:""， // 默认往当前地址提交请求
		type:"post",
		data:formdataobj,
		
		// 需要指定两个关键性参数
		contentType:false, //***********************
		processData:false,
		// 参数由后端返回
		success:functon(args){
			// 正确的跳转
			if (args.code == 1000){
				windows.location.href(args.url)
			}
			// 错误时应该将错误信息渲染出去
			else{
			$.each(args.errors, function(key, value){
				// 如何将错误信息给到span标签中？
				// 需要用到标签查找。注意到form组件渲染的input的id值为id_字段名，id_usrname
				// 给label 标签加上for="{{ form.auto_id}}，拿到input的id
				
				// 1.先成id，再利用jQuery查找
				let targetid = "#id" + key;
				$(targetid).next().text(value[0]),parent().addClass('has-error')
				// next便是其之后的span标签 parent为其上边的div标签(为其输入框添加属性)
			
			})
			}
		}
	})
})
// 给input框绑定焦点事件
$('input').focus(function(){
	// 选中iuput框时将错误信息去掉
	$(this).next().text('').parent().removeClass('has-error')
})
'''
# back_dic = {'code':1000, 'msg':'', 'url':'/login/'}
back_dic = {'code':2000, 'msg':form_obj.errors, 'url':'/login/'}
# form组件验证后的错误信息：{'usrname':['****this should not be null'], 'password':['*****this should not be null']}
return Jsonresponse(back_dic)
~~~

#### 处理文件

```python
# ajax 发送文件对象。
表中的字段
file_usr = models.FileField(upload_to='file/', default='file/default.png')
# 该字段接收文件对象并自动在相应路径存储文件，字段则对应文件路径，如file/111.png



```















