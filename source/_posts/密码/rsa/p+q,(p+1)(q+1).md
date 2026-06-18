---
title: "p+q,(p+1)(q+1)"
date: 2025-12-11 15:33:03
categories: "密码"
tags:
  - "密码"
  - "rsa"
cover: /img/backgrounds/bg-03.png
top_img: /img/backgrounds/bg-03.png
disableNunjucks: true
---



```plain
p+q:0x1232fecb92adead91613e7d9ae5e36fe6bb765317d6ed38ad890b4073539a6231a6620584cea5730b5af83a3e80cf30141282c97be4400e33307573af6b25e2ea
(p+1)(q+1) : 0x5248becef1d925d45705a7302700d6a0ffe5877fddf9451a9c1181c4d82365806085fd86fbaab08b6fc66a967b2566d743c626547203b34ea3fdb1bc06dd3bb765fd8b919e3bd2cb15bc175c9498f9d9a0e216c2dde64d81255fa4c05a1ee619fc1fc505285a239e7bc655ec6605d9693078b800ee80931a7a0c84f33c851740
e : 0xe6b1bee47bd63f615c7d0a43c529d219
d : 0x2dde7fbaed477f6d62838d55b0d0964868cf6efb2c282a5f13e6008ce7317a24cb57aec49ef0d738919f47cdcd9677cd52ac2293ec5938aa198f962678b5cd0da344453f521a69b2ac03647cdd8339f4e38cec452d54e60698833d67f9315c02ddaa4c79ebaa902c605d7bda32ce970541b2d9a17d62b52df813b2fb0c5ab1a5
enc_flag : 0x50ae00623211ba6089ddfae21e204ab616f6c9d294e913550af3d66e85d0c0693ed53ed55c46d8cca1d7c2ad44839030df26b70f22a8567171a759b76fe5f07b3c5a6ec89117ed0a36c0950956b9cde880c575737f779143f921d745ac3bb0e379c05d9a3cc6bf0bea8aa91e4d5e752c7eb46b2e023edbc07d24a7c460a34a9a
```



```plain
import gmpy2
from Crypto.Util.number import long_to_bytes

# 给定的十六进制数据
p_plus_q_hex = "1232fecb92adead91613e7d9ae5e36fe6bb765317d6ed38ad890b4073539a6231a6620584cea5730b5af83a3e80cf30141282c97be4400e33307573af6b25e2ea"
p1_q1_hex = "5248becef1d925d45705a7302700d6a0ffe5877fddf9451a9c1181c4d82365806085fd86fbaab08b6fc66a967b2566d743c626547203b34ea3fdb1bc06dd3bb765fd8b919e3bd2cb15bc175c9498f9d9a0e216c2dde64d81255fa4c05a1ee619fc1fc505285a239e7bc655ec6605d9693078b800ee80931a7a0c84f33c851740"
e_hex = "e6b1bee47bd63f615c7d0a43c529d219"
d_hex = "2dde7fbaed477f6d62838d55b0d0964868cf6efb2c282a5f13e6008ce7317a24cb57aec49ef0d738919f47cdcd9677cd52ac2293ec5938aa198f962678b5cd0da344453f521a69b2ac03647cdd8339f4e38cec452d54e60698833d67f9315c02ddaa4c79ebaa902c605d7bda32ce970541b2d9a17d62b52df813b2fb0c5ab1a5"
enc_flag_hex = "50ae00623211ba6089ddfae21e204ab616f6c9d294e913550af3d66e85d0c0693ed53ed55c46d8cca1d7c2ad44839030df26b70f22a8567171a759b76fe5f07b3c5a6ec89117ed0a36c0950956b9cde880c575737f779143f921d745ac3bb0e379c05d9a3cc6bf0bea8aa91e4d5e752c7eb46b2e023edbc07d24a7c460a34a9a"

# 将十六进制字符串转换为整数
p_plus_q = int(p_plus_q_hex, 16)
p1_q1 = int(p1_q1_hex, 16)
e = int(e_hex, 16)
d = int(d_hex, 16)
enc_flag = int(enc_flag_hex, 16)

print("计算 n = (p+1)(q+1) - (p+q) - 1")
n = p1_q1 - p_plus_q - 1
print(f"n = {n}")

# 验证 n 是否为正数
if n <= 0:
    print("错误：计算得到的 n 不是正数")
    exit()

# 验证 ed ≡ 1 mod φ(n)？这里我们可以通过计算 m^e mod n 再解密来验证，但更简单的是验证 m^(ed) ≡ m mod n
# 我们可以选择一个小的测试值，比如 m=2
test_m = 2
test_c = pow(test_m, e, n)
test_dec = pow(test_c, d, n)
if test_dec == test_m:
    print(f"验证通过：ed ≡ 1 mod φ(n)")
else:
    print(f"验证失败：ed 可能不与 1 mod φ(n) 同余")
    # 继续尝试解密，因为可能仍然可以解密

# 解密 flag
print("\n解密 flag...")
flag_int = pow(enc_flag, d, n)
print(f"解密后的整数: {flag_int}")

# 尝试转换为字节
flag_bytes = long_to_bytes(flag_int)
print(f"解密后的字节: {flag_bytes}")

# 尝试解码为字符串
try:
    flag_str = flag_bytes.decode('utf-8')
    print(f"Flag: {flag_str}")
except UnicodeDecodeError:
    print("无法解码为 UTF-8，尝试其他编码或直接输出 hex")
    print(f"Hex: {flag_bytes.hex()}")
    
# 另外，我们也可以尝试计算 p 和 q，虽然解密不需要
# 根据 p+q 和 n = p*q，可以解二次方程：x^2 - (p+q)x + n = 0
print("\n尝试分解 n 以获取 p 和 q...")
# 计算判别式 D = (p+q)^2 - 4n
D = p_plus_q * p_plus_q - 4 * n
sqrt_D, is_square = gmpy2.iroot(D, 2)
if is_square:
    print("D 是完全平方数，可以分解 n")
    sqrt_D = int(sqrt_D)
    p = (p_plus_q + sqrt_D) // 2
    q = (p_plus_q - sqrt_D) // 2
    print(f"p = {p}")
    print(f"q = {q}")
    # 验证 p*q == n
    if p * q == n:
        print("验证通过：p * q == n")
    else:
        print("错误：p * q != n")
else:
    print("D 不是完全平方数，无法通过二次方程分解")

```

