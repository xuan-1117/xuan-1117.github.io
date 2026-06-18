---
title: "misc"
date: 2025-12-11 15:26:54
categories: "MISC"
tags:
  - "MISC"
cover: /img/backgrounds/bg-05.png
top_img: /img/backgrounds/bg-05.png
disableNunjucks: true
---

dddvdvb文件类别识别

1. **File命令**

当文件后缀名无法准确显示而使文件无法打开时，在linux使用file+文件名可以识别出文件的后缀，修改正确后可打开

2. **查看文件头**

使用010可查看文件头类型，文件的16进制内容，头尾可能隐含flag

![](/assets/docs/MISC/misc/img-001.png)

3. **使用stegsolve**

File Format:文件格式

Data Extract:数据提取

Steregram Solve:立体试图 左右控制偏移

Frame Browser:帧浏览器

Image Combiner:拼图 图片拼接

4. **分离图片**

**有时候图片会包含其他的文件**

![](/assets/docs/MISC/misc/img-002.png)

先使用binwalk查看图片

binwalk -e pcat.bin //要分解的文件名

也可使用foremost分离文件，文件会放在output文件夹里

5. 图片内容不显示完全，高度或宽度被修改

使用以下工具

![](/assets/docs/MISC/misc/img-003.png)

```plain
python3 -m pip install -r requirements.txt
python3 main.py -i dabai.png
```

![](/assets/docs/MISC/misc/img-004.png)

去010修改文件高度和宽度



6. **恢复删除文件**

```plain
extundelete disk-image --restore-all
```

7. **查看图片隐写**

```plain
zsteg -a steg.png  
```

8. **盲水印**

```plain
python bwmforpy3.py decode day1.png day2.png flag.png –oldseed
python2 bwm.py decode day1.png day2.png flag.png
```



![](/assets/docs/MISC/misc/img-005.png)

1. **摩斯密码只有01**
2. **分离gif图片**

![](/assets/docs/MISC/misc/img-006.png)

**conver 分离文件路径 生成文件路径**

**montage //合并文件**

![](/assets/docs/MISC/misc/img-007.png)

**-tile是拼接时每行和每列的图片数，这里用x1，就是只一行**

**-geometry是首选每个图和边框尺寸，我们边框为0，图照原始尺寸即可**

1. **暴力破解压缩包**

**fcrackzip工具**

**	使用方法：fcrackzip -b -c -l -u**

![](/assets/docs/MISC/misc/img-008.png)

**rarcrack这个工具**

**rarcrack 1.rar --threads 1000 --type rar**





1. **文件倒转**

cd FileReverse-Tools

python3 FileReverse-Tools.py -i /home/kali/Desktop/ping

![](/assets/docs/MISC/misc/img-009.png)



1. **Snow隐写**

![](/assets/docs/MISC/misc/img-010.png)

# 流量分析
<u>usb流量</u>：8字节为键盘输入，4字节为鼠标

**1、键盘流量**

USB协议数据部分在Leftover Capture Data域中，数据长度为八个字节。其中键盘击键信息集中在第三个字节中。

**2、鼠标流量**

USB协议鼠标数据部分，在Leftover Capture Data域中，数据长度为四个字节。

其中第一个字节代表按键，当取0x00时，代表没有按键、为0x01时，代表按左键，为0x02时，代表当前按键为右键。第二个字节可以看成是一个signed byte类型，其最高位为符号位，当这个值为正时，代表鼠标水平右移多少像素，为负时，代表水平左移多少像素。第三个字节与第二字节类似，代表垂直上下移动的偏移。



<u>Wav音频文件</u>：使用Audacity查看频谱图

