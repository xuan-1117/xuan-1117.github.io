---
title: "xor1"
date: 2025-12-11 15:30:21
categories: "密码"
tags:
  - "密码"
cover: /img/backgrounds/bg-09.png
top_img: /img/backgrounds/bg-09.png
disableNunjucks: true
---



```plain
from secret import flag
from os import urandom

assert flag.startswith(b'XMan{' ) and flag.endswith(b'}')
key=urandom(16)

def padding(m):
    l = 16 - (len(m) % 16)
    return m+(b'\x00'*l)

flag=padding(flag)+urandom(10)

enc=b''
for i in range(len(flag)):
    enc+=(key[i%16]^flag[i]).to_bytes(1,'big')
print(enc.hex())

# d8db4398596f9123f9b70d6847ad6e14e1ef5ff6220cfe4f96c5520731c81c78a645cdd1aa9b95e54468
```



```plain
# 密文字符串
enc_hex = "d8db4398596f9123f9b70d6847ad6e14e1ef5ff6220cfe4f96c5520731c81c78a645cdd1aa9b95e54468"
enc = bytes.fromhex(enc_hex)
len_enc = len(enc)  # 密文总长度：42字节
len_m_pad = len_enc - 10  # padding后的flag长度：32字节（42-10）

# 初始化16字节密钥
key = [0] * 16

# 步骤1：利用已知前缀XMan{恢复key前5位
known_prefix = b'XMan{'
for i in range(len(known_prefix)):
    key[i] = enc[i] ^ known_prefix[i]

# 步骤2：临时解密前32字节，找到flag结尾}的位置
temp_data = bytes([enc[i] ^ key[i % 16] for i in range(len_m_pad)])
end_pos = temp_data.find(b'}')
if end_pos == -1:
    # 遍历查找}
    for i in range(len_m_pad):
        if temp_data[i] == ord(b'}'):
            end_pos = i
            break
if end_pos == -1:
    print("未找到flag结尾符}")
    exit()

# 步骤3：利用padding补0区域恢复key剩余位（data[i]=0 → key[i%16]=enc[i]）
for i in range(end_pos + 1, len_m_pad):
    key_idx = i % 16
    key[key_idx] = enc[i]

# 步骤4：用完整key解密整个data
data = bytes([enc[i] ^ key[i % 16] for i in range(len_enc)])

# 步骤5：提取并验证flag
flag = data[:end_pos + 1]
if flag.startswith(b'XMan{') and flag.endswith(b'}'):
    print("✅ 恢复的flag：", flag.decode('utf-8'))
else:
    print("❌ 恢复结果格式异常：", flag)

# 辅助输出（可选）
print("\n📌 完整密钥（十六进制）：", bytes(key).hex())
print("📌 解密后的padding数据：", data[:len_m_pad])
```



