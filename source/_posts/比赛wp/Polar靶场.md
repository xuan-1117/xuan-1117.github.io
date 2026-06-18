---
title: "Polar靶场"
date: 2025-12-10 08:51:48
categories: "比赛wp"
tags:
  - "比赛wp"
cover: /img/backgrounds/bg-18.png
top_img: /img/backgrounds/bg-18.png
disableNunjucks: true
---

### 简单rec
![](/assets/docs/比赛wp/Polar靶场/img-001.png)

输出函数：

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



命令执行函数：

system() 输出并返回最后一行shell结果。

exec() 不输出结果，返回最后一行shell结果，所有结果可以保存到一个返回的数组里面。

passthru() 只调用命令，把命令的运行结果原样地直接输出到标准输出设备上。（替换system）

shell_exec()

popen()/proc_open()



空格绕过

${IFS}

{IFS}$9

$IFS$9

重定向符：<>（但是不支持后面跟通配符）

水平制表符%09

%0a 回车

%0d换行

$IFS$1

%09(tab)

%20(space)

$IFS

方法一：sys=passthru('sort%09/flag');

方法二：?sys=echo%09`vi%09`  //使用引号转义

Swp

![](/assets/docs/比赛wp/Polar靶场/img-002.png)

这就是说我们需要post一个名为xdmtql的变量，然后这个变量不能是数组。不能与/sys.*nb/is正则匹配。但是结果的sys nb要在第一位。

我尝试了用回车绕过，因为.*有一个特质：

| . | **匹配除换行符 \n 之外的任何单字符**。要匹配 . ，请使用 \. 。 |
| --- | --- |
| * | **匹配前面的子表达式零次或多次**。要匹配 * 字符，请使用 \*。 |


这就说明，如果换行符应该是不会被检测的。但是还是错的。所以就用回溯绕过法

### 蜜雪冰城吉警店啊
![](/assets/docs/比赛wp/Polar靶场/img-003.png)

这题是完全前端，就是根本不发包。所以bp的方法就不行了。然后源码看了好久都没找到id=9的地方，并且我还尝试传入id这个参数，都是没用的。看了wp只要在element里面将任意一个id改成9再点击就可以了

![](/assets/docs/比赛wp/Polar靶场/img-004.png)

### Jwt
jwt是什么？？？

jwt全称Json Web Token，大概意思就是json格式的互联网令牌

jwt要解决的问题是：为多种终端设备，提供统一的、安全的令牌格式

jwt只是一个令牌格式而已，你可以把它存储到cookie，也可以存储到localstorage，没有任何限制！

jwt令牌可以出现在响应的任何一个地方，客户端和服务器自行约定即可

jwt的组成

完成组合为：header.payload.signature

header：令牌头部，记录了整个令牌的类型和签名算法

payload：令牌负荷，记录了保存的主体信息，比如你要保存的用户信息就可以放到这里

signature：令牌签名，按照头部固定的签名算法对整个令牌进行签名，该签名的作用是：保证令牌不被伪造和篡改

**header：**令牌头部，记录了整个令牌的类型和签名算法，json格式如下：

![](/assets/docs/比赛wp/Polar靶场/img-005.png)

 注：header生成方式就是把header部分使用base64 url编码即可

![](/assets/docs/比赛wp/Polar靶场/img-006.png)**payload**：jwt的主体信息，是一个JSON对象，它可以包含以下内容：

**signature**：是jwt的签名，保证了整个jwt不被篡改（因为有密钥）

解释完什么是JWT后，就开始解题了，这里需要用到两个工具，一个是[**https://jwt.io/**](https://jwt.io/)，另一个是**jwt-cracker，jwt.io是解析工具，cracker是破解密钥工具**

将JWT的值复制到**jwt.io**，查看jwt三部分的内容![](/assets/docs/比赛wp/Polar靶场/img-007.png)

破解一下该JWT的密钥值，得到密钥SYSA，再根据数据包中给的提示，将payload的username改为admin，最后把新的jwt值复制到请求包中直接发包就成![](/assets/docs/比赛wp/Polar靶场/img-008.png)

![](/assets/docs/比赛wp/Polar靶场/img-009.png)

![](/assets/docs/比赛wp/Polar靶场/img-010.png)

### 浮生日记
XSS，常见的payload：<script>alert(1)</script>

然后发现里面是需要闭合的，并且会过滤script。先闭合然后再双写script。payload："><scriscriptpt>alert(1)</scriscriptpt>

![](/assets/docs/比赛wp/Polar靶场/img-011.png)

$$

![](/assets/docs/比赛wp/Polar靶场/img-012.png)

Eval,var_dump,echo的区别

var_dump()会将传入的变量打印到页面（变量类型,变量长度和变量值）

eval()会执行传入到其中的php代码

echo输出的是最终的结果（变量类型和[字符串](https://so.csdn.net/so/search?q=%E5%AD%97%E7%AC%A6%E4%B8%B2&spm=1001.2101.3001.7020)）



$GLOBALS：引用全局作用域中可用的全部变量（一个包含了全部变量的全局组合数组。变量的名字就是数组的键），与所有其他超全局变量不同，$GLOBALS在PHP代码中任何地方总是可用的

global在PHP中的解析是：global的作用是定义全局变量,但是这个全局变量不是应用于整个网站,而是应用于当前页面,包括include或require的所有文件。

注：在函数体内定义的global变量,函数体外可以使用,在函数体外定义的global变量不能在函数体内使用

$GLOBALS：用于访问所有全局变量(来自全局范围的变量),即可以从PHP脚本中的任何范围访问的变量。

![](/assets/docs/比赛wp/Polar靶场/img-013.png)

### 签到题
![](/assets/docs/比赛wp/Polar靶场/img-014.png)

伪协议读文件，使用路径穿越，双写绕过



### Session文件包含
进题随便输入一个数据，然后进到/action.php。用php伪协议读取action.php的源码，我去读取了，源码如下![](/assets/docs/比赛wp/Polar靶场/img-015.png)

session包含。我们在name中上传一句话木马，并且在file中包含这个文件就可以了。这个文件的目录一般在tmp中，而且命名为：**sess_PHPSESSID，而这个sessid的位置，一般在cookie里面储存：**![](/assets/docs/比赛wp/Polar靶场/img-016.png)

action.php?file=/tmp/sess_lr5v4rvv8t66gr4rog728smb91

访问目录便会执行上传的木马

php very nice

![](/assets/docs/比赛wp/Polar靶场/img-017.png)

1、__construct()

触发机制：使用关键字new实例化对象时会自动调用构造方法

参数：个数不限

作用：完成一些对象的初始化工作。常用的比如实现数据库自动连接。

将构造函数声明为私有方法，可以防止在类外部创建对象，这在[单例模式](https://so.csdn.net/so/search?q=%E5%8D%95%E4%BE%8B%E6%A8%A1%E5%BC%8F&spm=1001.2101.3001.7020)中经常使用。

![](/assets/docs/比赛wp/Polar靶场/img-018.png)

2、如果子类中定义了构造函数则不会隐式调用其父类的构造函数。要执行父类的构造函数，需要在子类的构造函数中调用**parent::__construct()**。如果子类没有定义构造函数则会如同一个普通的类方法一样从父类继承（假如没有被定义为 private 的话）。

第一种方法：$a->sys="system('tac f*');";

第二种：![](/assets/docs/比赛wp/Polar靶场/img-019.png)

PHP反序列化测试

![](/assets/docs/比赛wp/Polar靶场/img-020.png)

__toString()的作用

当我们调试程序时，需要知道是否得出正确的数据。比如打印一个对象时，看看这个对象都有哪些属性，其值是什么，如果类定义了toString方法，就能在测试时，echo打印对象体，对象就会自动调用它所属类定义的toString方法，格式化输出这个对象所包含的数据。

![](/assets/docs/比赛wp/Polar靶场/img-021.png)

Easy.__wakeup -> Evil.__toString

![](/assets/docs/比赛wp/Polar靶场/img-022.png)

审计

![](/assets/docs/比赛wp/Polar靶场/img-023.png)

[**MD5 0e 绕过**](https://blog.csdn.net/qq_38603541/article/details/97108663)

**MD5**是一种常见的哈希算法，用于将任意长度的输入转换为固定长度的128位哈希值。然而，MD5算法存在一些漏洞，其中之一就是**0e绕过**。这种绕过方式利用了PHP在处理哈希字符串时的特性，即将以**0e**开头的哈希值解释为0，从而导致不同的字符串在比较时被认为是相同的。

**0e 绕过的原理**

当两个MD5值进行弱类型比较时，如果它们的哈希值以**0e**开头且后面都是数字，那么PHP会将它们解释为0，从而认为它们相等。例如：

<?php

$a ="QNKCDZO";

$b ="240610708";

echo md5($a) ."\n";// 输出 0e830400451993494058024219903391

echo md5($b) . "\n"; // 输出 0e462097431906509019562988736854

var_dump(md5($a) == md5($b));// 输出 bool(true)

?>

在上述代码中，虽然$a和$b的原始值不同，但它们的MD5值在进行弱类型比较时被

认为是相同的![](/assets/docs/比赛wp/Polar靶场/img-024.png)

使用严格比较运算符（===）可以避免0e绕过的问题，因为严格比较会考虑类型。

0e开头的md5和原值：

QNKCDZO

0e830400451993494058024219903391

240610708

0e462097431906509019562988736854

s878926199a

0e545993274517709034328855841020

s155964671a

0e342768416822451524974117254469

s214587387a

0e848240448830537924465865611904

s214587387a

0e848240448830537924465865611904

s878926199a

0e545993274517709034328855841020

s1091221200a

0e940624217856561557816327384675

s1885207154a

0e509367213418206700842008763514

s1502113478a

0e861580163291561247404381396064

s1885207154a

0e509367213418206700842008763514

s1836677006a

0e481036490867661113260034900752

s155964671a

0e342768416822451524974117254469

s1184209335a

0e072485820392773389523109082030

s1665632922a

0e731198061491163073197128363787

s1502113478a

0e861580163291561247404381396064

s1836677006a

0e481036490867661113260034900752

s1091221200a

0e940624217856561557816327384675

s155964671a

0e342768416822451524974117254469

s1502113478a

0e861580163291561247404381396064

s155964671a

0e342768416822451524974117254469

s1665632922a

0e731198061491163073197128363787

s155964671a

0e342768416822451524974117254469

s1091221200a

0e940624217856561557816327384675

s1836677006a

0e481036490867661113260034900752

s1885207154a

0e509367213418206700842008763514

s532378020a

0e220463095855511507588041205815

s878926199a

0e545993274517709034328855841020

s1091221200a

0e940624217856561557816327384675

s214587387a

0e848240448830537924465865611904

s1502113478a

0e861580163291561247404381396064

s1091221200a

0e940624217856561557816327384675

s1665632922a

0e731198061491163073197128363787

s1885207154a

0e509367213418206700842008763514

s1836677006a

0e481036490867661113260034900752

s1665632922a

0e731198061491163073197128363787

s878926199a

0e545993274517709034328855841020

Bllbl_ser1

### 反序列化
![](/assets/docs/比赛wp/Polar靶场/img-025.png)

![](/assets/docs/比赛wp/Polar靶场/img-026.png)

### 套娃浏览器
一、SSRF是什么？

SSRF(Server-Side Request Forgery:服务器端请求伪造) 是一种由攻击者构造形成由服务端发起请求的一个安全漏洞。



一般情况下，SSRF攻击的目标是从外网无法访问的内部系统。（正是因为它是由服务端发起的，所以它能够请求到与它相连而与外网隔离的内部系统）



二、SSRF漏洞原理

SSRF 形成的原因大都是由于服务端提供了从其他服务器应用获取数据的功能且没有对目标地址做过滤与限制。![](/assets/docs/比赛wp/Polar靶场/img-027.png)





比如,黑客操作服务端从指定URL地址获取网页文本内容，加载指定地址的图片，下载等等。利用的是服务端的请求伪造。ssrf是利用存在缺陷的web应用作为代理攻击远程和本地的服务器

当我们发现SSRF漏洞后，首先要做的事情就是测试所有可用的URL伪协议



file:/// 从文件系统中获取文件内容，如，file:///etc/passwd

dict:// 字典服务器协议，访问字典资源，如，dict:///ip:6739/info：

sftp:// SSH文件传输协议或安全文件传输协议

ldap:// 轻量级目录访问协议

tftp:// 简单文件传输协议

gopher:// 分布式文档传递服务，可使用gopherus生成payload![](/assets/docs/比赛wp/Polar靶场/img-028.png)





#### <font style="color:rgba(0, 0, 0, 0.85);"></font>
### <font style="color:rgba(0, 0, 0, 0.85);">rapyiquan</font>
<font style="color:rgb(183, 189, 250);background-color:rgb(32, 35, 62);">传参是c_md,_被过滤，可以用[代替</font>

<font style="color:rgb(183, 189, 250);background-color:rgb(32, 35, 62);">cat可以用右斜杠绕过过滤</font>

![](/assets/docs/比赛wp/Polar靶场/img-029.png)

### 到底给不给flag
```plain
<?php
highlight_file('1.txt');
echo "<br><br>";

$flag = 'flag{f73da0c8e7c774d488a6df0fec2890d9}';
$qwq= '我想要flag';
$QAQ = '我又不想要flag了，滚吧';
if(!isset($_GET['flag']) && !isset($_POST['flag'])){
    exit($qwq);
}

if($_POST['flag'] === 'flag' || $_GET['flag'] === 'flag'){
    exit($QAQ);
}

foreach ($_POST as $key => $value) {
    $$key = $value;
}

foreach ($_GET as $key => $value) {
    $$key = $$value;
}

echo $flag;

```

存在变量赋值漏洞

:::info
foreach ($_GET as $key => $value) {

    $$key = $$value;

:::

`<font style="color:rgb(244, 116, 102);background-color:rgba(27, 31, 35, 0.05);">foreach()</font>`<font style="color:rgb(106, 115, 125);">配合</font>`<font style="color:rgb(244, 116, 102);background-color:rgba(27, 31, 35, 0.05);">$$</font>`<font style="color:rgb(106, 115, 125);">是个典型的变量覆盖漏洞，使用</font>`<font style="color:rgb(244, 116, 102);background-color:rgba(27, 31, 35, 0.05);">foreach()</font>`<font style="color:rgb(106, 115, 125);">来遍历数组中的值，然后再将获取到的数组键名作为变量，数组中的键值作为变量的值。例如传入</font>`<font style="color:rgb(244, 116, 102);background-color:rgba(27, 31, 35, 0.05);">abc=def</font>`<font style="color:rgb(106, 115, 125);">，就会变成</font>`<font style="color:rgb(244, 116, 102);background-color:rgba(27, 31, 35, 0.05);">$abc=$def</font>`

<font style="color:rgb(106, 115, 125);">因此解题关键为定义一个变量来保存原来</font>`<font style="color:rgb(244, 116, 102);background-color:rgba(27, 31, 35, 0.05);">$flag</font>`<font style="color:rgb(106, 115, 125);">的内容</font>

<font style="color:rgb(106, 115, 125);">再让</font>`<font style="color:rgb(244, 116, 102);background-color:rgba(27, 31, 35, 0.05);">$flag</font>`<font style="color:rgb(106, 115, 125);">=存储内容的变量名</font>

`<font style="color:rgb(244, 116, 102);background-color:rgba(27, 31, 35, 0.05);">abc=flag&flag=abc</font>`<font style="color:rgb(106, 115, 125);">会被解释为</font>`<font style="color:rgb(244, 116, 102);background-color:rgba(27, 31, 35, 0.05);">$abc=$flag&$flag=$abc</font>`

### <font style="color:rgba(0, 0, 0, 0.85);">phpurl</font>
![](/assets/docs/比赛wp/Polar靶场/img-030.png)

url二次编码

![](/assets/docs/比赛wp/Polar靶场/img-031.png)

### <font style="color:rgba(0, 0, 0, 0.85);">ezphp</font>
扫目录发现robots

![](/assets/docs/比赛wp/Polar靶场/img-032.png)

![](/assets/docs/比赛wp/Polar靶场/img-033.png)

### <font style="color:rgba(0, 0, 0, 0.85);"></font>


