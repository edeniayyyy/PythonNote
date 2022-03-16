## APIView的响应流程

APIView继承自View类

1. 路由匹配--> APIView.as_view()-->self.dispach   [本质上是这个函数]
2. APIView中重写了dispach，按照属性的访问顺序执行该函数。并且使用了crsf_exempt()避免了中间件的认证。按自己的方法认证
3. dispach中用了drf的Request对象改写了原来的request对象。

~~~python

# APIView的disapth
def dispatch(self, request, *args, **kwargs):
    self.args = args
    self.kwargs = kwargs
    # request参数是当次请求的request对象
    # 请求过来 wsgi注入一个envrion字段 django把它包装成一个request对象
    
    # 然后重新赋值的request 是初始化处理后的一个新的Request对象
    request = self.initialize_request(request, *args, **kwargs)
    
    # 现在视图函数拿到的request 已经不是django原生给我们封装的request
    # 而是drf自己定义的request对象(mro) 以后视图函数再使用的request对象 就是这个新的drf定义的对象
    self.request = request  # 将新的request对象赋值给self.request 你视图函数里面 其他的方法 也可以从这个里面获取相关数据
    self.headers = self.default_response_headers  # deprecate?

    try:
        # 三大认证模块
        # 该函数进行验证
        self.initial(request, *args, **kwargs)

        # Get the appropriate handler method
        if request.method.lower() in self.http_method_names:
            handler = getattr(self, request.method.lower(),
                              self.http_method_not_allowed)
        else:
            handler = self.http_method_not_allowed
		 # 响应模块
        response = handler(request, *args, **kwargs)

    except Exception as exc:
       # 异常模块
        response = self.handle_exception(exc)
	
    # 渲染模块
    self.response = self.finalize_response(request, response, *args, **kwargs)
    return self.response

~~~

