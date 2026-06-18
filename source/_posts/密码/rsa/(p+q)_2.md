---
title: "(p+q)_2"
date: 2025-12-11 15:33:34
categories: "密码"
tags:
  - "密码"
  - "rsa"
cover: /img/backgrounds/bg-22.png
top_img: /img/backgrounds/bg-22.png
disableNunjucks: true
---



```plain
import libnum
from Crypto.Util import number
from secret import flag


size = 256
e = 65537
p = number.getPrime(size)
q = number.getPrime(size)
avg = (p+q)/2
n = p*q

m = libnum.s2n(flag)
c = pow(m, e, n)

print('n = %d' % n)
print('avg = %d' % avg)
print('c = %d' % c)

```



```plain
n = 5700102857084805454304483466349768960970728516788155745115335016563400814300152521175777999545445613444815936222559357974566843756936687078467221979584601
avg = 75635892913589759545076958131039534718834447688923830032758709253942408722875
c = 888629627089650993173073530112503758717074884215641346688043288414489462472394318700014742820213053802180975816089493243275025049174955385229062207064503
```





```plain
import math
import libnum

# 给定数据
n = 5700102857084805454304483466349768960970728516788155745115335016563400814300152521175777999545445613444815936222559357974566843756936687078467221979584601
avg = 75635892913589759545076958131039534718834447688923830032758709253942408722875
c = 888629627089650993173073530112503758717074884215641346688043288414489462472394318700014742820213053802180975816089493243275025049174955385229062207064503
e = 65537

# 计算 p+q = 2*avg
s = 2 * avg

# 计算判别式 D = s^2 - 4n
D = s*s - 4*n

# 使用Python的math.isqrt计算整数平方根（Python 3.8+）
sqrt_D = math.isqrt(D)

# 验证是否是完全平方数
if sqrt_D * sqrt_D != D:
    # 如果不是完全平方数，尝试寻找精确的平方根
    # 对于大整数，我们可以使用牛顿法
    # 但在这个问题中，理论上D应该是完全平方数
    print("D is not a perfect square!")
    print(f"D = {D}")
    print(f"sqrt_D = {sqrt_D}")
    print(f"sqrt_D^2 = {sqrt_D * sqrt_D}")
    print(f"Difference = {D - sqrt_D * sqrt_D}")
    exit()

print(f"√D = {sqrt_D}")

# 计算 p 和 q
p = (s + sqrt_D) // 2
q = (s - sqrt_D) // 2

print(f"p = {p}")
print(f"q = {q}")

# 验证 p*q == n
if p * q != n:
    print("Error: p * q != n")
    exit()

print(f"p * q = {p * q}")
print(f"n = {n}")
print("Verification passed!")

# 计算 phi 和私钥 d
phi = (p-1) * (q-1)

# 使用扩展欧几里得算法计算模逆
def egcd(a, b):
    if b == 0:
        return a, 1, 0
    else:
        g, x1, y1 = egcd(b, a % b)
        x = y1
        y = x1 - (a // b) * y1
        return g, x, y

# 计算 d = e^(-1) mod phi
g, d, _ = egcd(e, phi)
if g != 1:
    print("e and phi are not coprime!")
    exit()

# 确保 d 是正数
d = d % phi
if d < 0:
    d += phi

print(f"d = {d}")

# 解密 c
m = pow(c, d, n)
print(f"Decrypted message (as integer): {m}")

# 将整数转换为字符串
flag = libnum.n2s(m)
print("Flag:", flag.decode())

```

