---
title: "porloo主线wp-1"
date: 2025-12-10 09:16:46
categories: "比赛wp"
tags:
  - "比赛wp"
cover: /img/backgrounds/bg-19.jpg
top_img: /img/backgrounds/bg-19.jpg
disableNunjucks: true
---

# 应急响应2-1


![](/assets/docs/比赛wp/porloo主线wp-1/img-001.jpeg)

[<font style="color:#0366D6;">http://192.168.20.100 </font>](http://192.168.20.100/)admin Skills@2020

登录jumpserver<sub>堡垒机：</sub>标签列表中可以看到flag

![](/assets/docs/比赛wp/porloo主线wp-1/img-002.jpeg)

palu{2025_qiandao_flag}



**应急响应**

**2-2**

![](/assets/docs/比赛wp/porloo主线wp-1/img-003.jpeg)

ubuntu/Skills@waf

[<font style="color:#0366D6;">https://192.168.20.102:9443/</font> ](https://192.168.20.102:9443/)admin/VF6NXMs7

点击waf的身份认证功能模块中的配置按钮

![](/assets/docs/比赛wp/porloo主线wp-1/img-004.jpeg)

可以看到flag

![](/assets/docs/比赛wp/porloo主线wp-1/img-005.jpeg)

palu{2025_waf}

# 应急响应2-3


![](/assets/docs/比赛wp/porloo主线wp-1/img-006.jpeg)

ubuntu/Skills@mysql [<font style="color:#0366D6;">http://192.168.20.101:20221/4a883f0c56</font>](http://192.168.20.101:20221/4a883f0c56) 64a44f8137/f8941f8eb7 root/mysql_QPiS8y

![](/assets/docs/比赛wp/porloo主线wp-1/img-007.jpeg)

连接上数据库就可以看到flag

palu{Mysql_@2025}

# 应急响应2-4


![](/assets/docs/比赛wp/porloo主线wp-1/img-008.jpeg)

有waf，我们可以在waf中看攻击防护：

![](/assets/docs/比赛wp/porloo主线wp-1/img-009.jpeg)

palu{192.168.20.107}

# 应急响应2-5


![](/assets/docs/比赛wp/porloo主线wp-1/img-010.jpeg)

还是刚刚的waf就能看到：

![](/assets/docs/比赛wp/porloo主线wp-1/img-011.jpeg)

2025-05-05-00:04:40

比较坑的一个地方就是题目给的flag格式为palu{xxxx-xx-xx-xx-xx-xx}，误导了好多师傅，我看到

这个地方也以为是提交的攻击时间有问题，而没去怀疑格式， 后快结束的半个小时才在群里发出来提示公告(当时已经无心做题了) palu{2025-05-05-00:04:40}

# 应急响应2-6


![](/assets/docs/比赛wp/porloo主线wp-1/img-012.jpeg)

一开始的思路是看waf中的未拦截攻击有无线索，结果发现没啥线索，想了一下，可能waf都没有

侦测到，于是直接登上了waf的服务器查看web服务器的日志有无记录

ps -aux    发现开启了nginx服务器，那就直接先找nginx的日志 sudo find / -name nginx

![](/assets/docs/比赛wp/porloo主线wp-1/img-013.jpeg)

可以把nginx的日志下载到桌面然后逐个分析

![](/assets/docs/比赛wp/porloo主线wp-1/img-014.jpeg)

发现了泄露了这个敏感文件/bak/key.txt

![](/assets/docs/比赛wp/porloo主线wp-1/img-015.jpeg)

可以访问看一下：[ <font style="color:#0366D6;">http://192.168.20.102/bak/key.txt</font>](http://192.168.20.102/bak/key.txt)

![](/assets/docs/比赛wp/porloo主线wp-1/img-016.jpeg)

palu{key.txt}

记录一下ssh密钥登录的一些知识点：

在远程登录服务器或访问其他需要 SSH 认证的服务时，可使用该密钥对进行身份验证。将公钥配

置在服务器上，私钥保留在本地。当本地客户端尝试连接服务器时，服务器会向客户端发送一个

加密的随机字符串，客户端使用私钥对其进行解密，然后将解密后的数据发送给服务器进行验

证。如果验证成功，客户端就可以无需输入密码而直接登录到服务器，这种方式比传统的密码验

证更为安全和便捷。

在企业业务服务器使用密钥认证的场景中，A 刚入职主管问 A 要的通常是公钥。具体过程如下：

1. A 在自己的设备上生成密钥对，包括公钥和私钥。
2. A 将生成的公钥提供给主管，主管把公钥配置到业务服务器的授权文件中

（如 ~/.ssh/authorized_keys）。

1. A 使用自己的私钥通过客户端发起登录请求，服务端会通过公钥加密一个随机字符串，客户

端再通过私钥解密该字符串返回给服务端，通过对比两个字符串是否一致来认证身份。

业务服务器上会有很多公钥，每个需要登录服务器的用户都有自己的公钥被存储在服务器上，以

此来实现基于密钥的身份认证，这种方式比传统的账号密码认证更安全，可避免密码在网络上传

输带来的安全风险

这里想写一些关于雷池waf的东西：

关于我们拿到题目进行应急的时候，waf是非常需要关注的东西，因为雷池waf可以看到有哪些地

址经过了waf防护，并且访问日志也保存到了waf中。

在雷池waf的防护应用中可以看到有哪些应用被接入了雷池waf中

![](/assets/docs/比赛wp/porloo主线wp-1/img-017.jpeg)

我们 好查看每个应用的详情按钮查看这个应用对应的地址是什么，方便了解服务

比如这个题，其实解法就在192.168.20.102这个应用中

点击详情后，应用的访问日志和错误日志都能查看

![](/assets/docs/比赛wp/porloo主线wp-1/img-018.jpeg)

好点击下载到本地查看(不清楚为啥下载的日志比实时查看的内容要全很多，可能是日志太大的缘故，网页显示不全)

![](/assets/docs/比赛wp/porloo主线wp-1/img-019.jpeg)

从waf中也能看到本题的答案，而且更加直观且有逻辑性

除此之外还要看看是否代理了其他服务器中的应用，比如现在这个192.168.20.103:9999<sub>这个应用</sub>

的访问日志就在这里看

![](/assets/docs/比赛wp/porloo主线wp-1/img-020.jpeg)

# 应急响应2-7


![](/assets/docs/比赛wp/porloo主线wp-1/img-021.jpeg)

也是刚刚那个

[<font style="color:#0366D6;">http://192.168.20.102/bak/key.txt</font>](http://192.168.20.102/bak/key.txt)

![](/assets/docs/比赛wp/porloo主线wp-1/img-022.jpeg)

palu{parloo@parloo.com}



**应急响应**

**2-8**

![](/assets/docs/比赛wp/porloo主线wp-1/img-023.jpeg)

**立足点**（Footprint）在网络安全领域通常指： 

攻击者通过渗透测试或攻击手段，在目标网络中成功控制的**第一台主机或服务器**。这台服务器成

为攻击者进一步渗透内部网络的**跳板或基地**，因此被称为 “立足点服务器”。

其实内网几台主机的ip，试试就能出来，看了很多wp，估计都是试出来的

我强行解释一波

在雷池waf中，192.168.20.108是资产表中的sshServer主机的地址，这个主机是我们的资产，且在 waf中留下了它很多次攻击，所以它就是立足点服务器ip地址

![](/assets/docs/比赛wp/porloo主线wp-1/img-024.jpeg)

palu{192.168.20.108}

# 应急响应2-9


![](/assets/docs/比赛wp/porloo主线wp-1/img-025.jpeg)

前面已经判断出立足点服务器是192.168.20.108了，所以需要登录上192.168.20.108查看攻击者创

建的用户名，再利用查出来的/etc/shadow破解出密码来

![](/assets/docs/比赛wp/porloo主线wp-1/img-026.jpeg)

palu{parloo/parloo}

但是我这里有点问题，就是shell工具就是连接不上192.168.20.108，直接在虚拟机中连接，速度

也超级慢，输入密码后一直卡着，我ctrl+c了一下，卡进去了，但是没有交互页面，做一些操作就

会不舒服，但我看有的师傅的wp，好像并没有受到影响

![](/assets/docs/比赛wp/porloo主线wp-1/img-027.jpeg)

可能是只允许密钥登录的问题，给他改一下配置：

![](/assets/docs/比赛wp/porloo主线wp-1/img-028.jpeg)

更改配置后重启，发现shell工具依旧远程连接不了，就连接上之后一直这样卡着

![](/assets/docs/比赛wp/porloo主线wp-1/img-029.jpeg)

就算得使用虚拟机连接都需要卡出去一下才能连接成功

![](/assets/docs/比赛wp/porloo主线wp-1/img-030.jpeg)

# 应急响应2-10


![](/assets/docs/比赛wp/porloo主线wp-1/img-031.jpeg)

![](/assets/docs/比赛wp/porloo主线wp-1/img-032.jpeg)

顺手发现了flag

palu{hi_2025_parloo_is_hack}

# 应急响应2-11


![](/assets/docs/比赛wp/porloo主线wp-1/img-033.jpeg)

我们知道了提权后的账号parloo/parloo，其实可以直接su parloo使用它的高权限去操作，更舒服

一些，可以输入下history看看黑客有没有留下什么日志

一般的维权手段就是木马，netstat -anplt查看一下服务器的外联地址，发现了

![](/assets/docs/比赛wp/porloo主线wp-1/img-034.jpeg)

原本我只是没有看到pid，想用ubuntu权限看一下是否能看到pid，结果发现两个权限使用netstat

## -anplt得到的回显是不同的
![](/assets/docs/比赛wp/porloo主线wp-1/img-035.jpeg)

我们直接使用sudo netstat -anplt查看下运行结果

![](/assets/docs/比赛wp/porloo主线wp-1/img-036.jpeg)

发现了外联ip启动恶意程序为b4b40c4ws，使用

但是题目问的是维权方法服务的名称，需要找服务名称

我们先看看b4b40c4ws在哪？

![](/assets/docs/比赛wp/porloo主线wp-1/img-037.jpeg)

/usr/bin/b4b40c44ws 因为考虑到我们的sshserver机器是重启之后的，重启之后还有这个恶意外联，所以可能存在定时

任务或服务自启动项，因为题目问的是维权方法服务的名称，所以大概率就是使用的服务自启动

项

查看 PID 对应的 systemd 服务有两种方法

直接使用命令查看：

systemctl status pid<sub>号，可以发现两个维权服务</sub>

![](/assets/docs/比赛wp/porloo主线wp-1/img-038.jpeg)

我们可以简单分析下这个服务：

使用命令：systemctl cat <<sub>服务名</sub>>    <sub>或者是</sub>cat /etc/systemd/system/xxx.service 如果配置了 Restart=always，那该服务就会在退出后**一直重启**。

![](/assets/docs/比赛wp/porloo主线wp-1/img-039.jpeg)

palu{rootset}



**应急响应**

**2-12**

![](/assets/docs/比赛wp/porloo主线wp-1/img-040.jpeg)

上一题详细分析了

palu{47.101.213.153} 我在复现过程中发现了一个问题，就是有时候netstat -anplt发现不了恶意外联，有时候又可以发

现，于是这道题我就想用分析维权木马的方式来找出恶意服务器连接地址。

因为sshserver机器的问题，不能直接导出恶意木马来丢到微步或者自己用ida分析，于是我想着将 b4b40c44ws<sub>搞到桌面来分析</sub>现在20.108机器中开启python的http服务，然后再用物理机下载

![](/assets/docs/比赛wp/porloo主线wp-1/img-041.jpeg)

下载后直接将b4b40c44ws拖入ida分析，反汇编后直接可以看到ip地址

![](/assets/docs/比赛wp/porloo主线wp-1/img-042.jpeg)

于是这样也可以得到flag:

palu{47.101.213.153} 丢到微步也是一样的：

![](/assets/docs/比赛wp/porloo主线wp-1/img-043.jpeg)

果然，有师傅上周六都打到这里了，tql

![](/assets/docs/比赛wp/porloo主线wp-1/img-044.jpeg)



**应急响应**

**2-13**

![](/assets/docs/比赛wp/porloo主线wp-1/img-045.jpeg)

终使用这条命令排查出系统中被劫持的程序 ls -alh /usr/bin /bin /usr/sbin /sbin | grep -vE 'root|staff|bin'

这条命令的意思是：在系统核心可执行目录中，找出“文件属主不是常规系统用户”的可疑程序。

这些目录包含绝大多数的系统命令：

在 /bin、/usr/bin 等系统命令目录中，**绝大部分程序都应该属于 root（或者极少数属于 bin、 staff**<sub>**）**</sub><sub>。</sub>

/bin

：基础命令，如

ls、cp、mv...

/usr/bin

：扩展命令，如

vim、nano、

python3

...

/sbin

：系统管理命令，如

ifconfig

、

reboot

/usr/sbin

：更多的管理命令，如

sshd

、

apache2



⚠

如果你看到有文件的拥有者是：

ubuntu、www-data、nobody某个普通用户UID 不是0（非 root）

那说明这个程序**不是由系统正常安装的**，可能是攻击者放进去的伪装程序、后门、木马。

![](/assets/docs/比赛wp/porloo主线wp-1/img-046.jpeg)

palu{id}

# 应急响应2-14


![](/assets/docs/比赛wp/porloo主线wp-1/img-047.jpeg)

因为牵扯到服务，所以又得把实现转回到server<sub>服务器，即</sub>192.168.20.103<sub>这台服务器，使用</sub>fscan 直接扫，看一下开启了哪些服务

![](/assets/docs/比赛wp/porloo主线wp-1/img-048.jpeg)

访问web服务器时，发现8081端口开启的服务： [<font style="color:#0366D6;">http://192.168.20.103:8081/</font>](http://192.168.20.103:8081/)

![](/assets/docs/比赛wp/porloo主线wp-1/img-049.jpeg)

泄露了员工的的身份证号跟手机号，110，woc，京✌

本来做到这里还想着是不是还能泄露其他人的额信息，用bp抓包发现根本没有其他路由，F12发现，这玩意竟然时写死进去的，6

![](/assets/docs/比赛wp/porloo主线wp-1/img-050.jpeg)

palu{8081}

# 应急响应2-15


![](/assets/docs/比赛wp/porloo主线wp-1/img-051.jpeg)

上一题可以得到：

palu{310105198512123456}



**应急响应**

**2-16**

![](/assets/docs/比赛wp/porloo主线wp-1/img-052.jpeg)

<font style="color:#000000;">	</font>[<font style="color:#0366D6;">http://192.168.20.102:3000/</font>](http://192.168.20.102:3000/%E7%9B%B4%E6%8E%A5504%E4%BA%86%EF%BC%8C%E7%BB%99%E6%88%91%E5%90%93%E4%B8%80%E8%B7%B3%EF%BC%8C%E6%88%91%E5%AF%BB%E6%80%9D%E6%98%AF%E6%88%91%E6%BC%8F%E6%89%AB%E7%BB%99%E6%9C%8D%E5%8A%A1%E6%89%93%E5%B4%A9%E4%BA%86%E5%91%A2%EF%BC%8C%E5%8F%88%E9%87%8D%E6%96%B0%E5%AF%BC%E5%85%A5%E4%BA%86%E4%B8%8B%E8%99%9A%E6%8B%9F%E6%9C%BA%E5%90%8E%E5%8F%91%E7%8E%B0%E8%BF%98%E6%98%AF504%EF%BC%8C%E6%88%91%E4%B8%80%E6%83%B3%EF%BC%8C%E5%8E%9F%E6%9D%A5%E6%98%AFmysql%E6%9C%8D%E5%8A%A1%E5%99%A8%E6%B2%A1%E5%BC%80%E5%90%AF%E7%9A%84%E5%8E%9F%E5%9B%A0)<font style="color:#0366D6;">	</font>[<font style="color:#0366D6;">504</font>](http://192.168.20.102:3000/%E7%9B%B4%E6%8E%A5504%E4%BA%86%EF%BC%8C%E7%BB%99%E6%88%91%E5%90%93%E4%B8%80%E8%B7%B3%EF%BC%8C%E6%88%91%E5%AF%BB%E6%80%9D%E6%98%AF%E6%88%91%E6%BC%8F%E6%89%AB%E7%BB%99%E6%9C%8D%E5%8A%A1%E6%89%93%E5%B4%A9%E4%BA%86%E5%91%A2%EF%BC%8C%E5%8F%88%E9%87%8D%E6%96%B0%E5%AF%BC%E5%85%A5%E4%BA%86%E4%B8%8B%E8%99%9A%E6%8B%9F%E6%9C%BA%E5%90%8E%E5%8F%91%E7%8E%B0%E8%BF%98%E6%98%AF504%EF%BC%8C%E6%88%91%E4%B8%80%E6%83%B3%EF%BC%8C%E5%8E%9F%E6%9D%A5%E6%98%AFmysql%E6%9C%8D%E5%8A%A1%E5%99%A8%E6%B2%A1%E5%BC%80%E5%90%AF%E7%9A%84%E5%8E%9F%E5%9B%A0)

[去访问](http://192.168.20.102:3000/%E7%9B%B4%E6%8E%A5504%E4%BA%86%EF%BC%8C%E7%BB%99%E6%88%91%E5%90%93%E4%B8%80%E8%B7%B3%EF%BC%8C%E6%88%91%E5%AF%BB%E6%80%9D%E6%98%AF%E6%88%91%E6%BC%8F%E6%89%AB%E7%BB%99%E6%9C%8D%E5%8A%A1%E6%89%93%E5%B4%A9%E4%BA%86%E5%91%A2%EF%BC%8C%E5%8F%88%E9%87%8D%E6%96%B0%E5%AF%BC%E5%85%A5%E4%BA%86%E4%B8%8B%E8%99%9A%E6%8B%9F%E6%9C%BA%E5%90%8E%E5%8F%91%E7%8E%B0%E8%BF%98%E6%98%AF504%EF%BC%8C%E6%88%91%E4%B8%80%E6%83%B3%EF%BC%8C%E5%8E%9F%E6%9D%A5%E6%98%AFmysql%E6%9C%8D%E5%8A%A1%E5%99%A8%E6%B2%A1%E5%BC%80%E5%90%AF%E7%9A%84%E5%8E%9F%E5%9B%A0)	[<font style="color:#0366D6;">直接</font>](http://192.168.20.102:3000/%E7%9B%B4%E6%8E%A5504%E4%BA%86%EF%BC%8C%E7%BB%99%E6%88%91%E5%90%93%E4%B8%80%E8%B7%B3%EF%BC%8C%E6%88%91%E5%AF%BB%E6%80%9D%E6%98%AF%E6%88%91%E6%BC%8F%E6%89%AB%E7%BB%99%E6%9C%8D%E5%8A%A1%E6%89%93%E5%B4%A9%E4%BA%86%E5%91%A2%EF%BC%8C%E5%8F%88%E9%87%8D%E6%96%B0%E5%AF%BC%E5%85%A5%E4%BA%86%E4%B8%8B%E8%99%9A%E6%8B%9F%E6%9C%BA%E5%90%8E%E5%8F%91%E7%8E%B0%E8%BF%98%E6%98%AF504%EF%BC%8C%E6%88%91%E4%B8%80%E6%83%B3%EF%BC%8C%E5%8E%9F%E6%9D%A5%E6%98%AFmysql%E6%9C%8D%E5%8A%A1%E5%99%A8%E6%B2%A1%E5%BC%80%E5%90%AF%E7%9A%84%E5%8E%9F%E5%9B%A0)<font style="color:#0366D6;"> </font>[<font style="color:#0366D6;">了，给我吓一跳，我寻思是我漏扫给服务打崩了呢，</font>](http://192.168.20.102:3000/%E7%9B%B4%E6%8E%A5504%E4%BA%86%EF%BC%8C%E7%BB%99%E6%88%91%E5%90%93%E4%B8%80%E8%B7%B3%EF%BC%8C%E6%88%91%E5%AF%BB%E6%80%9D%E6%98%AF%E6%88%91%E6%BC%8F%E6%89%AB%E7%BB%99%E6%9C%8D%E5%8A%A1%E6%89%93%E5%B4%A9%E4%BA%86%E5%91%A2%EF%BC%8C%E5%8F%88%E9%87%8D%E6%96%B0%E5%AF%BC%E5%85%A5%E4%BA%86%E4%B8%8B%E8%99%9A%E6%8B%9F%E6%9C%BA%E5%90%8E%E5%8F%91%E7%8E%B0%E8%BF%98%E6%98%AF504%EF%BC%8C%E6%88%91%E4%B8%80%E6%83%B3%EF%BC%8C%E5%8E%9F%E6%9D%A5%E6%98%AFmysql%E6%9C%8D%E5%8A%A1%E5%99%A8%E6%B2%A1%E5%BC%80%E5%90%AF%E7%9A%84%E5%8E%9F%E5%9B%A0)

[<font style="color:#0366D6;">504</font>](http://192.168.20.102:3000/%E7%9B%B4%E6%8E%A5504%E4%BA%86%EF%BC%8C%E7%BB%99%E6%88%91%E5%90%93%E4%B8%80%E8%B7%B3%EF%BC%8C%E6%88%91%E5%AF%BB%E6%80%9D%E6%98%AF%E6%88%91%E6%BC%8F%E6%89%AB%E7%BB%99%E6%9C%8D%E5%8A%A1%E6%89%93%E5%B4%A9%E4%BA%86%E5%91%A2%EF%BC%8C%E5%8F%88%E9%87%8D%E6%96%B0%E5%AF%BC%E5%85%A5%E4%BA%86%E4%B8%8B%E8%99%9A%E6%8B%9F%E6%9C%BA%E5%90%8E%E5%8F%91%E7%8E%B0%E8%BF%98%E6%98%AF504%EF%BC%8C%E6%88%91%E4%B8%80%E6%83%B3%EF%BC%8C%E5%8E%9F%E6%9D%A5%E6%98%AFmysql%E6%9C%8D%E5%8A%A1%E5%99%A8%E6%B2%A1%E5%BC%80%E5%90%AF%E7%9A%84%E5%8E%9F%E5%9B%A0)<font style="color:#0366D6;">	</font>[<font style="color:#0366D6;">mysql 又重新导入了下虚拟机后发现还是</font>](http://192.168.20.102:3000/%E7%9B%B4%E6%8E%A5504%E4%BA%86%EF%BC%8C%E7%BB%99%E6%88%91%E5%90%93%E4%B8%80%E8%B7%B3%EF%BC%8C%E6%88%91%E5%AF%BB%E6%80%9D%E6%98%AF%E6%88%91%E6%BC%8F%E6%89%AB%E7%BB%99%E6%9C%8D%E5%8A%A1%E6%89%93%E5%B4%A9%E4%BA%86%E5%91%A2%EF%BC%8C%E5%8F%88%E9%87%8D%E6%96%B0%E5%AF%BC%E5%85%A5%E4%BA%86%E4%B8%8B%E8%99%9A%E6%8B%9F%E6%9C%BA%E5%90%8E%E5%8F%91%E7%8E%B0%E8%BF%98%E6%98%AF504%EF%BC%8C%E6%88%91%E4%B8%80%E6%83%B3%EF%BC%8C%E5%8E%9F%E6%9D%A5%E6%98%AFmysql%E6%9C%8D%E5%8A%A1%E5%99%A8%E6%B2%A1%E5%BC%80%E5%90%AF%E7%9A%84%E5%8E%9F%E5%9B%A0)<font style="color:#0366D6;"> </font>[<font style="color:#0366D6;">，我一想，原来是</font>](http://192.168.20.102:3000/%E7%9B%B4%E6%8E%A5504%E4%BA%86%EF%BC%8C%E7%BB%99%E6%88%91%E5%90%93%E4%B8%80%E8%B7%B3%EF%BC%8C%E6%88%91%E5%AF%BB%E6%80%9D%E6%98%AF%E6%88%91%E6%BC%8F%E6%89%AB%E7%BB%99%E6%9C%8D%E5%8A%A1%E6%89%93%E5%B4%A9%E4%BA%86%E5%91%A2%EF%BC%8C%E5%8F%88%E9%87%8D%E6%96%B0%E5%AF%BC%E5%85%A5%E4%BA%86%E4%B8%8B%E8%99%9A%E6%8B%9F%E6%9C%BA%E5%90%8E%E5%8F%91%E7%8E%B0%E8%BF%98%E6%98%AF504%EF%BC%8C%E6%88%91%E4%B8%80%E6%83%B3%EF%BC%8C%E5%8E%9F%E6%9D%A5%E6%98%AFmysql%E6%9C%8D%E5%8A%A1%E5%99%A8%E6%B2%A1%E5%BC%80%E5%90%AF%E7%9A%84%E5%8E%9F%E5%9B%A0)<font style="color:#0366D6;"> </font>[<font style="color:#0366D6;">服务器没开启的原因</font>](http://192.168.20.102:3000/%E7%9B%B4%E6%8E%A5504%E4%BA%86%EF%BC%8C%E7%BB%99%E6%88%91%E5%90%93%E4%B8%80%E8%B7%B3%EF%BC%8C%E6%88%91%E5%AF%BB%E6%80%9D%E6%98%AF%E6%88%91%E6%BC%8F%E6%89%AB%E7%BB%99%E6%9C%8D%E5%8A%A1%E6%89%93%E5%B4%A9%E4%BA%86%E5%91%A2%EF%BC%8C%E5%8F%88%E9%87%8D%E6%96%B0%E5%AF%BC%E5%85%A5%E4%BA%86%E4%B8%8B%E8%99%9A%E6%8B%9F%E6%9C%BA%E5%90%8E%E5%8F%91%E7%8E%B0%E8%BF%98%E6%98%AF504%EF%BC%8C%E6%88%91%E4%B8%80%E6%83%B3%EF%BC%8C%E5%8E%9F%E6%9D%A5%E6%98%AFmysql%E6%9C%8D%E5%8A%A1%E5%99%A8%E6%B2%A1%E5%BC%80%E5%90%AF%E7%9A%84%E5%8E%9F%E5%9B%A0)

gitea	mysql 因为 的数据库用的 服务器

![](/assets/docs/比赛wp/porloo主线wp-1/img-053.jpeg)

看到题目后一脸懵，不知道问的啥，看了wp，提到了gitee，突然想起前几天看到的马 [<font style="color:#0366D6;">http://192.168.20.102:3000/</font>](http://192.168.20.102:3000/)

gitea，老熟人了，很多打集团的思路，遇到gitea就是翻代码，代码中藏了很多敏感信息，有时候翻完，啥都有了

![](/assets/docs/比赛wp/porloo主线wp-1/img-054.jpeg)

发现gitea只有这一个项目



![](/assets/docs/比赛wp/porloo主线wp-1/img-055.jpeg)

是一个

go

写的代码，简单看一下代码逻辑：通过

/admin/parloo

路径提供

 Web 

界面，支持

 GET

（显示表单）和

 POST

（执行命令）请求。

![](/assets/docs/比赛wp/porloo主线wp-1/img-056.jpeg)

[<font style="color:#0366D6;">http://192.168.20.103:8080/admin/parloo</font><sub><font style="color:#0366D6;">这个路径就存在危险功能</font></sub><font style="color:#0366D6;">(</font><sub><font style="color:#0366D6;">可以进行命令执行</font></sub><font style="color:#0366D6;">)</font>](http://192.168.20.103:8080/admin/parloo%E8%BF%99%E4%B8%AA%E8%B7%AF%E5%BE%84%E5%B0%B1%E5%AD%98%E5%9C%A8%E5%8D%B1%E9%99%A9%E5%8A%9F%E8%83%BD(%E5%8F%AF%E4%BB%A5%E8%BF%9B%E8%A1%8C%E5%91%BD%E4%BB%A4%E6%89%A7%E8%A1%8C))

![](/assets/docs/比赛wp/porloo主线wp-1/img-057.jpeg)

palu{/admin/parloo}

# 应急响应2-17


![](/assets/docs/比赛wp/porloo主线wp-1/img-058.jpeg)

资产图中明确告诉了近源机器

![](/assets/docs/比赛wp/porloo主线wp-1/img-059.jpeg)

找到恶意程序，其实我们思路可以先看外联ip，外联ip对应的pid对应哪个程序，这个程序大概率

就是恶意程序

用户名这么露骨，一时间我都不知道该登录那个账号了

![](/assets/docs/比赛wp/porloo主线wp-1/img-060.jpeg)

登录后发现又内网通，其实我 先想的是先去内网通看看有没有历史记录，桌面又有个文件名交 hasher，也不像好人，都可以去翻翻

![](/assets/docs/比赛wp/porloo主线wp-1/img-061.jpeg)

上了火绒剑看了下，虽然找到了木马文件，但是并未找到符合题解的flag，看了网上的wp，在

C:\Windows\Temp\<sub>找到了</sub>svhost.exe

![](/assets/docs/比赛wp/porloo主线wp-1/img-062.jpeg)

放了微步上发现周六有师傅就已经做到这一题了

![](/assets/docs/比赛wp/porloo主线wp-1/img-063.jpeg)

palu{0f80a82621b8c4c3303d198d13776b34}



**应急响应**

**2-18**

![](/assets/docs/比赛wp/porloo主线wp-1/img-064.jpeg)

上一题看到了恶意账户hack hack使用md5加密：

![](/assets/docs/比赛wp/porloo主线wp-1/img-065.jpeg)

palu{d78b6f30225cdc811adfe8d4e7c9fd34}



**应急响应**

**2-19**

![](/assets/docs/比赛wp/porloo主线wp-1/img-066.jpeg)

刚刚的近源机器虽然看到了内网通，但是并没有任何记录，这个时候考虑登录其他两个pc，看看上面的内网通有无题目说的flag

![](/assets/docs/比赛wp/porloo主线wp-1/img-067.jpeg)

palu{nbq_nbq_parloo}

# 应急响应2-20


![](/assets/docs/比赛wp/porloo主线wp-1/img-068.jpeg)

维护页面就是之前看到的：

[<font style="color:#0366D6;">http://192.168.20.103:8080/admin/parloo</font>](http://192.168.20.103:8080/admin/parloo)

还要结合之前在gitea看到的维护页面的源码

维护页面源码中写了日志保存到command.log页面

![](/assets/docs/比赛wp/porloo主线wp-1/img-069.jpeg)

我们去20.103那个服务器上去找command.log在哪

![](/assets/docs/比赛wp/porloo主线wp-1/img-070.jpeg)

反正文件也不多，可以挨个去看

/command.log /opt/parloo/command.log /var/log/parloo/command.log

![](/assets/docs/比赛wp/porloo主线wp-1/img-071.jpeg)

palu{Server_Parloo_2025}



**应急响应**

**2-21**

![](/assets/docs/比赛wp/porloo主线wp-1/img-072.jpeg)

题目应该或许大概可以理解成攻击者使用维护页面第一次命令执行的时间

![](/assets/docs/比赛wp/porloo主线wp-1/img-073.jpeg)

2025-05-04 15:30:38 palu{2025-05-04:15:30:38}



**应急响应**

**2-22**

![](/assets/docs/比赛wp/porloo主线wp-1/img-074.jpeg)

还是刚刚那个维护页面的命令执行日志

![](/assets/docs/比赛wp/porloo主线wp-1/img-075.jpeg)

palu{10.12.12.13:9999}

# 应急响应2-23


![](/assets/docs/比赛wp/porloo主线wp-1/img-076.jpeg)

想起了近源机器中那个重要的数据： c3a1c3c13e326020c3919093e1260525045e

![](/assets/docs/比赛wp/porloo主线wp-1/img-077.jpeg)

但是cmd5并没有查到

而且是36位的数据，并不是常规的32位

![](/assets/docs/比赛wp/porloo主线wp-1/img-078.jpeg)

看了wp，有点考察思维的

因为是近源那台机器的数据嘛，近源那台机器账号为hack 恰好gitea中也有个叫hack的用户

![](/assets/docs/比赛wp/porloo主线wp-1/img-079.jpeg)

我们尝试登录hack

想起了数据库我们是可以看到的

看这里的时候想着需要有个对照的账户，所以我又注册了一个账号

test    12345678

查看数据库的user表：

发现了一个flag，好呀，还有意外之喜 palu{crP1ZIVfqrkfdhGy}    <sub>其实真实做题的时候就可以全提交一遍了，反正又不限制错误次数</sub>

![](/assets/docs/比赛wp/porloo主线wp-1/img-080.jpeg)

passwd肯定是加密过的，我们先保存下hack的passwd：

bfd762fa77fbe2cb55c81878fa343ed754464cf59a98d79e3baaea62d17f67ed57bb3404d2400f0c0

45d93588fc6f81ede73

然后用test<sub>的</sub>passwd<sub>替换掉</sub>hack<sub>的</sub>passwd

替换为之后发现还是不能登录，考

hack<sub>的</sub>salt<sub>值：</sub>6b887cf34c01f71b9c656a1c42d3c5cb test<sub>的</sub>salt<sub>值：</sub>61896254a0eb8ee9954eb776f5346fee 发现salt的值不一样，也给他替换了，替换完之后，成功登录

![](/assets/docs/比赛wp/porloo主线wp-1/img-081.jpeg)

hack有个私有库，标明了，是加密的恶意脚本

![](/assets/docs/比赛wp/porloo主线wp-1/img-082.jpeg)

加密脚本如下，让ai帮咱们写出解密脚本来

![](/assets/docs/比赛wp/porloo主线wp-1/img-083.jpeg)

这个提交周期也应该注意下，有时候时间可以帮助你筛查一些东西：

![](/assets/docs/比赛wp/porloo主线wp-1/img-084.jpeg)

可以直接把加密脚本给ai，让ai给逆向算法，但是实际操作时发现解密也需要key，但是我们又不

知道key，我们给ai一些可能的条件，比如明文的前几个字符大概率时palu{， 有一个字符是}，

但是ai也只能通过这些条件解密出部分key来，其余的要么猜出key来，要么爆破出key来

![](/assets/docs/比赛wp/porloo主线wp-1/img-085.jpeg)

可以证明key的前几个字符为MySec，看了wp， 后的key是MySecretKey 解密脚本：



def custom_decrypt(encrypted_text, key):

 

    decrypted = []

 


    key_bytes = [ord(c) for c in key]  # 

将密钥转换为

ASCII

数值列表

 

    # 

将加密字符串按两个字符分割成字节列表

 

    encrypted_bytes = [

 


        int(encrypted_text[i:i+2], 16) 

 


        for i in range(0, len(encrypted_text), 2)

 


    ]

 


    for i, byte in enumerate(encrypted_bytes):

 


        # 

步骤

1

：逆向置换（交换高

4

位和低

4

位）

 

        substituted = byte

 


        xored = ((substituted & 0x0F) << 4) | ((substituted & 0xF0) >> 4)

 


        # 

步骤

2

：逆向异或操作（使用相同密钥）

 

        xor_key = key_bytes[i % len(key_bytes)]

 


        shifted = xored ^ xor_key

 


        # 

步骤

3

：逆向位移（注意处理负数）

 

        shift = (i % 5) + 1  # 

原始位移量计算

 

        original = shifted - shift

 


        # 

处理

ASCII

溢出（保证在

0-255

范围内）

 

        if original < 0:

 


            original += 256

 


        decrypted.append(chr(original))

 


    return ''.join(decrypted)

 


# 

使用示例

----------------------------

 

encrypted_str = "c3a1c3c13e326020c3919093e1260525045e"

 

key = "MySecretKey"

 

# 

解密并打印结果

 

print(custom_decrypt(encrypted_str, key))

有了key，瞬间就能出flag： palu{Password-000}

后续已知key是11位的情况下，我让gpt给我写一个可以爆破key的脚本：

其实比赛的时候完全可以用性能好的服务器去累加式的等价key的位数来爆破出可见字符来

![](/assets/docs/比赛wp/porloo主线wp-1/img-086.jpeg)

# 应急响应2-24


![](/assets/docs/比赛wp/porloo主线wp-1/img-087.jpeg)

恶意维权软件，他既然说软件了，大概率就是windows主机，之前在近源机器中发现启动项有一

个未知文件，丢到微步显示是木马

![](/assets/docs/比赛wp/porloo主线wp-1/img-088.jpeg)

做题的时候建议svhost.exe跟svhost都试试，真不一定是哪个，有时候明明你看到了答案，但是因

为格式的问题，你就会提交不成功 palu{svhost}

# 应急响应2-25


![](/assets/docs/比赛wp/porloo主线wp-1/img-089.jpeg)

使用everything查看黑客大概入侵事件内有无可以文件被更改

![](/assets/docs/比赛wp/porloo主线wp-1/img-090.jpeg)

看到了这个ipconfig.exe，之前分析维工具的时候就看到了这个python文件，太显眼了，一般都是

有问题的文件。本来想着运行下直接看外联地址，结果发现exe根本无法运行，一运行就报错。只

能反编译分析一下了。

python反编译的过程，总体逻辑就两步：

1、将.exe反编译成.pyc的二进制文件 2、将.pyc文件反编译成.py文件首先需要下载pyinstxtractor.py [<font style="color:#0366D6;">https://github.com/extremecoders-re/pyinstxtractor</font>](https://github.com/extremecoders-re/pyinstxtractor)

![](/assets/docs/比赛wp/porloo主线wp-1/img-091.jpeg)

发现了敏感文件

![](/assets/docs/比赛wp/porloo主线wp-1/img-092.jpeg)

直接去在线网站进行pyc反编译

[<font style="color:#0366D6;">https://tool.lu/pyc</font>](https://tool.lu/pyc)

![](/assets/docs/比赛wp/porloo主线wp-1/img-093.jpeg)

'server': '88.173.90.103', 'port': 22, 'username': 'ubuntu', 'password': 'OOWPWPWADADA' 得到flag<sub>：</sub>

palu{88.173.90.103}



**应急响应**

**2-26**

![](/assets/docs/比赛wp/porloo主线wp-1/img-094.jpeg)

之前在做维护页面命令执行日志查看的时候发现了一些dnslog地址，尝试选几个提交

![](/assets/docs/比赛wp/porloo主线wp-1/img-095.jpeg)

palu{np85qqde.requestrepo.com}

# 应急响应2-27


![](/assets/docs/比赛wp/porloo主线wp-1/img-096.jpeg)

想起之前用fscan对server服务器进行了一次漏扫，好像看到了监控综合安防平台，想起了之前海

康的综合安防平台就一直报反序列化漏洞，所以尝试提交9999端口，发现提交flag成功

![](/assets/docs/比赛wp/porloo主线wp-1/img-097.jpeg)

palu{9999}

这里闲来无事纠结一下。fscan没有扫描到反序列化漏洞，于是我上了afrong

![](/assets/docs/比赛wp/porloo主线wp-1/img-098.jpeg)

发现扫描除了shiro框架然后又有heapdump，这时候我就开始考虑，是不是通过heapdump拿到

了shiro的key，然后打的shiro反序列化呢？

反正比赛也打完了，那就玩玩呗。

访问[<font style="color:#0366D6;">http://192.168.20.103:9999/actuator/heapdump，拿到heapdump </font>](http://192.168.20.103:9999/actuator/heapdump%EF%BC%8C%E6%8B%BF%E5%88%B0heapdump)使用jdumpspider提取heapdump中的敏感信息

![](/assets/docs/比赛wp/porloo主线wp-1/img-099.jpeg)

## 发现了shirokey，果然，跟我预想的一样
algMode = CBC, key = QZYysgMYhG6/CzIJlVpR2g==, algName = AES

直接使用shiro利用工具去打

[<font style="color:#0366D6;">https://github.com/SummerSec/ShiroAttack2</font>](https://github.com/SummerSec/ShiroAttack2)

![](/assets/docs/比赛wp/porloo主线wp-1/img-100.jpeg)

果然，就是9999端口存在反序列化漏洞

# 应急响应2-28


![](/assets/docs/比赛wp/porloo主线wp-1/img-101.jpeg)

shirokey 上一题获取的

palu{QZYysgMYhG6/CzIJlVpR2g==}



**应急响应**

**2-29**

![](/assets/docs/比赛wp/porloo主线wp-1/img-102.jpeg)

wp	wp 想了半天没想出来，然后也没 ， 后在有位师傅的半成品 中有所体现

9999	103	9999 跟之前想的思路差不多就是找 端口访问日志，但是 这个机器中找不到 端口的访问日

志

在waf<sub>中找到了</sub>[<font style="color:#0366D6;">http://192.168.20.103:9999/</font><sub><font style="color:#0366D6;">的防护日志</font></sub>](http://192.168.20.103:9999/%E7%9A%84%E9%98%B2%E6%8A%A4%E6%97%A5%E5%BF%97)

![](/assets/docs/比赛wp/porloo主线wp-1/img-103.jpeg)

查看访问日志

![](/assets/docs/比赛wp/porloo主线wp-1/img-104.jpeg)

13/May/2025:16:45:19

根据题目要求整理flag格式： palu{2025/05/13:16:45:19}



**应急响应**

**2-30**

![](/assets/docs/比赛wp/porloo主线wp-1/img-105.jpeg)![](/assets/docs/比赛wp/porloo主线wp-1/img-106.jpeg)

parloohack

![](/assets/docs/比赛wp/porloo主线wp-1/img-107.jpeg)

将shadow信息处理好放入1.txt中

![](/assets/docs/比赛wp/porloo主线wp-1/img-108.jpeg)

john -format=crypt 1.txt

![](/assets/docs/比赛wp/porloo主线wp-1/img-109.jpeg)

palu{parloohack/123456}



**应急响应**

**2-31**

![](/assets/docs/比赛wp/porloo主线wp-1/img-110.jpeg)

上一题不是拿到了parloohack这个账号吗，直接使用账号密码登录这个账号，对这个账号的一些

信息进行应急排查

首先现在历史记录

![](/assets/docs/比赛wp/porloo主线wp-1/img-111.jpeg)

发现黑客写了一个恶意的服务，并且把这个服务作为开启自启动，是一种维权方式

palu{parloohack_script.service}



**应急响应**

**2-32**

![](/assets/docs/比赛wp/porloo主线wp-1/img-112.jpeg)

parloohack

## 还是	的日志
![](/assets/docs/比赛wp/porloo主线wp-1/img-113.jpeg)

<font style="color:#000000;">	</font>aa	md5

发现黑客对 这个可执行文件进行了 记录

<font style="color:#000000;">	</font>aa	md5sum

发现 就在当前文件下，使用	进行加密即可

![](/assets/docs/比赛wp/porloo主线wp-1/img-114.jpeg)

palu{4123940b3911556d4bf79196cc008bf4}

# 应急响应2-33


![](/assets/docs/比赛wp/porloo主线wp-1/img-115.jpeg)

看wp提示，又需要回到pc主机了(赛方人家也有的说啊，真实情况下，谁告诉你线索在哪台机器

上) 三台pc机器，其中palu02中有谷歌浏览器

![](/assets/docs/比赛wp/porloo主线wp-1/img-116.jpeg)

vshell<sub>，铁黑客无疑了，服务器地址也泄露出来了</sub>47.101.218.153 看了wp，需要在设置中搜索密码

![](/assets/docs/比赛wp/porloo主线wp-1/img-117.jpeg)

发现c2使用谷歌浏览器保存了密码点击右键那个三角查看详细信息的时候发现了flag

![](/assets/docs/比赛wp/porloo主线wp-1/img-118.jpeg)

47.101.213.153:8082

用户名：841366067 密码：pwn3rzs

palu{X5E1yklz1oAdyHBZ}

还有一个c2.com<sub>也注意下</sub>

万一用得上

![](/assets/docs/比赛wp/porloo主线wp-1/img-119.jpeg)

admin

admin@qwer



**应急响应**

**2-34**

![](/assets/docs/比赛wp/porloo主线wp-1/img-120.jpeg)

看了wp。vshell的用户名是qq号（我想过他是github的用户名，就没想过他是qq号，看来还是缺

点敏锐度）

![](/assets/docs/比赛wp/porloo主线wp-1/img-121.jpeg)

建议使用手机QQ去搜索，因为手机qq可以看到空间

![](/assets/docs/比赛wp/porloo主线wp-1/img-122.jpeg)

palu{ParlooSEc}



**应急响应**

**2-35**

![](/assets/docs/比赛wp/porloo主线wp-1/img-123.jpeg)

直接访问攻击者的github<sub>地址：</sub>

[<font style="color:#0366D6;">https://github.com/ParlooSEc/fffflllgggg</font>](https://github.com/ParlooSEc/fffflllgggg)

![](/assets/docs/比赛wp/porloo主线wp-1/img-124.jpeg)

palu{s5o3WkX33hptyJjk}



**应急响应**

**2-36**

![](/assets/docs/比赛wp/porloo主线wp-1/img-125.jpeg)

pc1启动时看到了好多恶意用户

![](/assets/docs/比赛wp/porloo主线wp-1/img-126.jpeg)

一共99<sub>个</sub>

palu{99}

# 应急响应2-37


![](/assets/docs/比赛wp/porloo主线wp-1/img-127.jpeg)

使用mimikatz之前，想着试几个弱口令，结果第一个就进去了

palu{123456}



**应急响应**

**2-38**

![](/assets/docs/比赛wp/porloo主线wp-1/img-128.jpeg)

之前在gitea<sub>数据库中找到的</sub>flag

palu{crP1ZIVfqrkfdhGy}

# 应急响应2-39


![](/assets/docs/比赛wp/porloo主线wp-1/img-129.jpeg)

hacker之前看了，没有东西，那估计就是admin这个账户藏着flag了，直接老样子，替换密码登录

admin

admin-password<sub>：</sub>

8231accbd6c278c47f9863b6e4e119f8c6994372e513c32ad89cb98f1b193b4e1425029b61e495bd fd11b20ccec3555642a5 admin-salt<sub>： </sub>b171d280fe6055840eb8153178a81cbc

早知道admin能看到所有的私有库信息，那就不改hack的密码了

![](/assets/docs/比赛wp/porloo主线wp-1/img-130.jpeg)

看到palu<sub>私有库：</sub>

![](/assets/docs/比赛wp/porloo主线wp-1/img-131.jpeg)

需要进行md5<sub>解密</sub>

![](/assets/docs/比赛wp/porloo主线wp-1/img-132.jpeg)

palu{FO65SruuTukdpBS5}

# 应急响应2-40


![](/assets/docs/比赛wp/porloo主线wp-1/img-133.jpeg)

这个题，给了提示，真好，让我们知道侦察方向是在mysql服务器中

常规看敏感目录、计划任务、外联ip均未发现异常整套题目做到了现在，我们已经可以理出时间线来了，5月2日是目前攻击 早的时间(对于整个应急题的时间线来说，主线题还要靠后。 晚时间就是15号，16号都已经发压缩包了)

查找其他可疑目录中修改的可执行文件：

sudo find /tmp /var/tmp /dev/shm /home /root -type f -perm /a+x  

( -newermt "2025-05-02" ! -newermt "2025-05-16" )  

-exec ls -la {} ; 2>/dev/null | sort -k6,7

![](/assets/docs/比赛wp/porloo主线wp-1/img-134.jpeg)

发现可疑文件/root/.a

palu{ba7c9fc1ff58b48d0df5c88d2fcc5cd1}



**应急响应**

**2-41**

![](/assets/docs/比赛wp/porloo主线wp-1/img-135.jpeg)

我们直接进入/root，权限还不够，所以先把它复制到我们可以控制的地方：

![](/assets/docs/比赛wp/porloo主线wp-1/img-136.jpeg)

ida<sub>打开反编译后发现</sub>simulate_network_communication<sub>跟通信有关</sub>

![](/assets/docs/比赛wp/porloo主线wp-1/img-137.jpeg)

palu{simulate_network_communication}

# 应急响应2-42


![](/assets/docs/比赛wp/porloo主线wp-1/img-138.jpeg)

还是看ida，直接取巧，因为文件名都是可见字符串嘛，直接shift+F12查看程序中的所有可见字符

串

![](/assets/docs/比赛wp/porloo主线wp-1/img-139.jpeg)

但其实看函数名字也能看出来，进入之后也能看到 palu{.malware_log.txt}

# 应急响应2-43


![](/assets/docs/比赛wp/porloo主线wp-1/img-140.jpeg)

ida直接看，懂一两个单词一眼就能看出来

![](/assets/docs/比赛wp/porloo主线wp-1/img-141.jpeg)

palu{simulate_privilege_escalation}

# 应急响应2-44


![](/assets/docs/比赛wp/porloo主线wp-1/img-142.jpeg)

之前看内网通的时候记得有个记录： 

![](/assets/docs/比赛wp/porloo主线wp-1/img-143.jpeg)

所以是Parloo-<sub>子怡被钓鱼了 </sub>palu{Parloo-<sub>子怡</sub>}

# 应急响应2-45


![](/assets/docs/比赛wp/porloo主线wp-1/img-144.jpeg)

接上一题，看到钓鱼文件的路径了：

![](/assets/docs/比赛wp/porloo主线wp-1/img-145.jpeg)

C:\Users\Public\Nwt\cache\recv\Parloo-<sub>沉沉</sub>\<sub>回执</sub>.exe palu{C:\Users\Public\Nwt\cache\recv\Parloo-<sub>沉沉</sub>}

# 应急响应2-46


![](/assets/docs/比赛wp/porloo主线wp-1/img-146.jpeg)

之前浏览器那个vshell的地址

palu{47.101.213.153}



**应急响应**

**2-47**

![](/assets/docs/比赛wp/porloo主线wp-1/img-147.jpeg)

就是之前浏览器拿到的c2.com的用户名和密码

admin admin@qwer palu{admin/admin@qwer}



# 应急响应1-1


![](/assets/docs/比赛wp/porloo主线wp-1/img-148.jpeg)

<font style="color:#24292E;">一般应急，我的思路是先看入口点(一般被攻击的情况下，就是从互联网端打进来的，所以着重看</font>

<font style="color:#24292E;">一下哪些机器是公网可以访问到的，一般这种机器都是突破口，后期的内网渗透也是拿这台机器</font>

<font style="color:#24292E;">当作跳板机)。但是整个做题过程中发现， 好不要这样，看了其他大佬的wp，发现很多题目的</font>

<font style="color:#24292E;">关键是在其他几个机器中找到的，所以接收下这个教训，不要死磕一两个机器，如果没有思路，</font>

<font style="color:#24292E;">一定要看完整个网络环境下的其他机器。</font>

<font style="color:#24292E;">按照题目给的拓扑来看：</font>

![](/assets/docs/比赛wp/porloo主线wp-1/img-149.jpeg)

<font style="color:#24292E;">整个网络拓扑图的入口点是在web server服务器</font>

<font style="color:#24292E;">使用netstat -anplt查看web server服务器对外开放了几个端口，web端口一般都是突破口</font>

<font style="color:#24292E;">本题webserver服务器开着80端口，跑着nginx服务器，所以找攻击者的攻击ip，一般是要看nginx 日志有无攻击记录</font>

![](/assets/docs/比赛wp/porloo主线wp-1/img-150.jpeg)

<font style="color:#24292E;">一般ubuntu服务器上的日志存放位置为/var/log中，我们看到了nginx目录，所以进入查看nginx 目录中有什么日志文件</font>

![](/assets/docs/比赛wp/porloo主线wp-1/img-151.jpeg)

<font style="color:#24292E;">访问日志是access.log，报错日志是error.log后面那个.1就是代表之前存的日志</font>

<font style="color:#24292E;">查看道这个ip192.168.31.240访问的这几个php文件就不正常，所以是攻击地址</font>

![](/assets/docs/比赛wp/porloo主线wp-1/img-152.jpeg)

<font style="color:#24292E;">palu{192.168.31.240}</font>

# 应急响应1-2


![](/assets/docs/比赛wp/porloo主线wp-1/img-153.jpeg)

<font style="color:#24292E;">这个题，其实可以套路下就能出答案。</font>

<font style="color:#24292E;">还是顺着正常流程去讲</font>

<font style="color:#24292E;">一般我们在看webserver这个互联网入口的web服务器，我们要知道这台服务器上开启了哪些web 服务，要都访问一下，做到心中有数，因为只有知道了具体的web服务，你才能在自己心中设想</font>

<font style="color:#24292E;">出，哪部分服务会有问题，可能出现什么攻击思路</font>

<font style="color:#24292E;">而且有时候，其实看一下历史记录往往会有一些意想不到的发现(你想呀，通过history，你也能知</font>

<font style="color:#24292E;">道运维部署了什么环境，建议拿到提目前先把root用户跟当前用户的history记录给down下来保存好，便分析边应急) 通过历史记录可以看到，运维的环境都是用docker部署的</font>

<font style="color:#24292E;">使用docker ps可以查看当前docker开启了哪些容器，分别占用了哪些端口</font>

![](/assets/docs/比赛wp/porloo主线wp-1/img-154.jpeg)

<font style="color:#24292E;">可以使用命令进入指定的容器名称 docker exec -it </font><sub><font style="color:#24292E;">容器</font></sub><font style="color:#24292E;">id /bin/sh</font>

![](/assets/docs/比赛wp/porloo主线wp-1/img-155.jpeg)

<font style="color:#24292E;">nc反连地址：</font>

<font style="color:#24292E;">palu{192.168.31.11}</font>



**<font style="color:#24292E;">应急响应</font>**

**<font style="color:#24292E;">1-3</font>**

![](/assets/docs/比赛wp/porloo主线wp-1/img-156.jpeg)

	<font style="color:#24292E;">docker	phpmyadmin</font>

<font style="color:#24292E;">题目在问暴力破解，看了 开启的几个环境，只有 容易有暴力破解的攻击场 phpmyadmin 景，但是进入 </font><sub><font style="color:#24292E;">容器也没找到日志</font></sub>

<font style="color:#24292E;">Docker 容器默认不会将日志写入磁盘文件（除非你特别配置了挂载）。它会把日志写到容器标准输出</font>

<font style="color:#24292E;">显示启动信息、访问日志、错误信息等</font>

<font style="color:#24292E;">docker logs phpmyadmin</font>

![](/assets/docs/比赛wp/porloo主线wp-1/img-157.jpeg)

<font style="color:#24292E;">短时间内，大量访问统一url</font><sub><font style="color:#24292E;">，还是</font></sub><font style="color:#24292E;">post</font><sub><font style="color:#24292E;">请求方式，请求的</font></sub><font style="color:#24292E;">url</font><sub><font style="color:#24292E;">也是</font></sub><font style="color:#24292E;">phpmyadmin</font><sub><font style="color:#24292E;">的登录</font></sub><font style="color:#24292E;">url</font>

<font style="color:#24292E;">palu{2025:03:05:58}</font>



**<font style="color:#24292E;">应急响应</font>**

**<font style="color:#24292E;">1-4</font>**

![](/assets/docs/比赛wp/porloo主线wp-1/img-158.jpeg)

<font style="color:#24292E;">此时需要用到pc机器了(做题卡在某个机器上一旦没思路的时候，一定要看其他机器，我看了web 服务后总感觉会藏flag，找了好长的时间没找到，也不想看其他机器了) win10 pc2</font><sub><font style="color:#24292E;">中直接用</font></sub><font style="color:#24292E;">everything</font><sub><font style="color:#24292E;">可以找到</font></sub><font style="color:#24292E;">flag1</font>

![](/assets/docs/比赛wp/porloo主线wp-1/img-159.jpeg)

<font style="color:#24292E;">palu{pc3_zgsfqwerlkssaw}</font>

![](/assets/docs/比赛wp/porloo主线wp-1/img-160.jpeg)

<font style="color:#24292E;">或者一般在windows应急时，大家比较关注任务计划，也能看到flag信息</font>

![](/assets/docs/比赛wp/porloo主线wp-1/img-161.jpeg)

# 应急响应1-5


![](/assets/docs/比赛wp/porloo主线wp-1/img-162.jpeg)

<font style="color:#24292E;">还是flag1</font><sub><font style="color:#24292E;">这个文件中</font></sub>

![](/assets/docs/比赛wp/porloo主线wp-1/img-163.jpeg)

## 含有a.bat这个文件，打开这个文件发现flag2
![](/assets/docs/比赛wp/porloo主线wp-1/img-164.jpeg)

<font style="color:#24292E;">flag2palu{nizhidaowoyouduoainima}</font>

# 应急响应1-6


![](/assets/docs/比赛wp/porloo主线wp-1/img-165.jpeg)

<font style="color:#24292E;">flag3</font><sub><font style="color:#24292E;">在</font></sub><font style="color:#24292E;">mysql</font><sub><font style="color:#24292E;">数据库中</font></sub>

![](/assets/docs/比赛wp/porloo主线wp-1/img-166.jpeg)

<font style="color:#24292E;">看到大佬的wp，真的细，不服不行。我当时就猜测肯定有个flag在数据库，这里我也看到了，就</font>

<font style="color:#24292E;">是简单的一扫而过</font>

![](/assets/docs/比赛wp/porloo主线wp-1/img-167.jpeg)

<font style="color:#24292E;">当时就感觉这个Mr.chen有问题，因为它的订单时间就跟攻击时间太吻合，一时没想起来这个点</font>

<font style="color:#24292E;">怎么利用</font>

![](/assets/docs/比赛wp/porloo主线wp-1/img-168.jpeg)

<font style="color:#24292E;">base64</font><sub><font style="color:#24292E;">解码后，发现</font></sub><font style="color:#24292E;">flag3</font>

<font style="color:#24292E;">palu{sqlaabbccsbwindows}</font>

<font style="color:#24292E;">其实，你在了解系统的时候，把订单都查查也能看到flag3</font>

![](/assets/docs/比赛wp/porloo主线wp-1/img-169.jpeg)

# 应急响应1-7


![](/assets/docs/比赛wp/porloo主线wp-1/img-170.jpeg)

<font style="color:#24292E;">如果细心翻一下回收站是可以看到钓鱼文件的</font>

![](/assets/docs/比赛wp/porloo主线wp-1/img-171.jpeg)

<font style="color:#24292E;">在实战中，其实都是hr跟客服容易被钓鱼，一般钓鱼文件要么是一些通知、要么就是简历等符合</font>

<font style="color:#24292E;">工作特性的文件</font>

![](/assets/docs/比赛wp/porloo主线wp-1/img-172.jpeg)

<font style="color:#24292E;">简历.zip被删了，在回收站中，还原即可 win计算文件md5的命令：</font>

<font style="color:#24292E;">certutil -hashfile "</font><sub><font style="color:#24292E;">文件路径</font></sub><font style="color:#24292E;">" md5</font>

![](/assets/docs/比赛wp/porloo主线wp-1/img-173.jpeg)

<font style="color:#24292E;">题目要求是大写，所以换成大写即可</font>

<font style="color:#24292E;">palu{2977CDAB8F3EE5EFDDAE61AD9F6CF203}</font>

# 应急响应1-8


![](/assets/docs/比赛wp/porloo主线wp-1/img-174.jpeg)

<font style="color:#24292E;">之前在web服务器中，看到nginx日志中，黑客ip访问了a.php，所以这个文件很敏感</font>

<font style="color:#24292E;">还有一种思路就是，黑客一般会在web服务器中传webshell(题目讲了)，我们分析web服务器各个端口开启的服务发现，使用的语言是php语言，所以我们可以查看下5月1号-5月2号这个期间web 服务器上有无修改过的php文件(应急响应一定要对时间敏感，有时候可以把时间当作筛查条件进行筛查)</font>

<font style="color:#24292E;">查找5月1日和5月2日修改过的PHP文件：</font>

<font style="color:#24292E;">find / -type f -name "*.php" -newermt "2025-05-01" ! -newermt "2025-05-03" 2>/dev/null</font>

![](/assets/docs/比赛wp/porloo主线wp-1/img-175.jpeg)

<font style="color:#24292E;">执行后可以发现这几个文件</font>

![](/assets/docs/比赛wp/porloo主线wp-1/img-176.jpeg)

<font style="color:#24292E;">一下可以查看两个webshell的木马 palu{hack}</font>

# 应急响应1-9


![](/assets/docs/比赛wp/porloo主线wp-1/img-177.jpeg)

<font style="color:#24292E;">详细解看有的师傅放出来的wp</font>

[<font style="color:#0366D6;">https://blog.csdn.net/qq_30817059/article/details/148061612?</font>](https://blog.csdn.net/qq_30817059/article/details/148061612?sharetype=blogdetail&shareId=148061612&sharerefer=APP&sharesource=qq_30817059&sharefrom=qq)

[<font style="color:#0366D6;">sharetype=blogdetail&shareId=148061612&sharerefer=APP&sharesource=qq_30817059&share from=qq</font>](https://blog.csdn.net/qq_30817059/article/details/148061612?sharetype=blogdetail&shareId=148061612&sharerefer=APP&sharesource=qq_30817059&sharefrom=qq)



**<font style="color:#24292E;">应急响应</font>**

**<font style="color:#24292E;">1-10</font>**

![](/assets/docs/比赛wp/porloo主线wp-1/img-178.jpeg)

<font style="color:#24292E;">palu{00232}</font>



**<font style="color:#24292E;">应急响应</font>**

**<font style="color:#24292E;">1-11</font>**

![](/assets/docs/比赛wp/porloo主线wp-1/img-179.jpeg)

<font style="color:#24292E;">使用D</font><sub><font style="color:#24292E;">盾发现了隐藏账户</font></sub><font style="color:#24292E;">system$</font>

![](/assets/docs/比赛wp/porloo主线wp-1/img-180.jpeg)

<font style="color:#24292E;">使用mimikatz</font><sub><font style="color:#24292E;">跑出</font></sub><font style="color:#24292E;">hash</font><sub><font style="color:#24292E;">来</font></sub><font style="color:#24292E;">提取 SAM & SYSTEM 文件手动离线分析，从系统中导出 </font><font style="color:#24292E;">SAM</font><font style="color:#24292E;"> 和 </font><font style="color:#24292E;">SYSTEM</font><font style="color:#24292E;"> 文件，然后用 mimikatz</font>

<font style="color:#24292E;">离线读取：导出命令(cmd管理员权限启动)：</font>

<font style="color:#24292E;">reg save hklm\sam sam.save </font>

<font style="color:#24292E;">reg save hklm\system system.save</font>

<font style="color:#24292E;">然后在 mimikatz </font><sub><font style="color:#24292E;">中执行：</font></sub>

<font style="color:#24292E;">privilege::debug lsadump::sam /sam:sam.save /system:system.save</font>

![](/assets/docs/比赛wp/porloo主线wp-1/img-181.jpeg)

<font style="color:#24292E;">Hash NTLM: dbae99beb48fd9132e1cf77f4c746979</font>

## 去md5网站进行查询
![](/assets/docs/比赛wp/porloo主线wp-1/img-182.jpeg)

<font style="color:#24292E;">据说解密出来的值为： wmx_love 反过来加密可以验证，对的上</font>

![](/assets/docs/比赛wp/porloo主线wp-1/img-183.jpeg)

<font style="color:#24292E;">palu{wmx_love}</font>

# 应急响应1-12


![](/assets/docs/比赛wp/porloo主线wp-1/img-184.jpeg)

<font style="color:#24292E;">通过逆向手段拿到了黑客的id</font>

<font style="color:#24292E;">这个黑客id在github上有仓库，可以泄露出邮箱来：</font>

<font style="color:#24292E;">使用api接口可以进行查询：通过获取目标 Github 用户名，替换下面链接中的</font><font style="color:#24292E;"><name></font><font style="color:#24292E;"> 进行查询：</font>

[<font style="color:#0366D6;">https://api.github.com/users/</font>](https://api.github.com/users/)<font style="color:#24292E;">/events/public 访问：</font>[<font style="color:#24292E;"> </font>](https://api.github.com/users/n0k4u/events/public)[<font style="color:#0366D6;">https://api.github.com/users/n0k4u/events/public</font>](https://api.github.com/users/n0k4u/events/public)

![](/assets/docs/比赛wp/porloo主线wp-1/img-185.jpeg)

<font style="color:#24292E;">参考文章：</font>

[<font style="color:#0366D6;">https://blog.csdn.net/fishfishfishman/article/details/141320003</font>](https://blog.csdn.net/fishfishfishman/article/details/141320003)

<font style="color:#24292E;">邮箱号：</font>

<font style="color:#0366D6;">n0k4u@outlook.com </font><font style="color:#24292E;">palu{n0k4u@outlook.com}</font>



**<font style="color:#24292E;">应急响应</font>**

**<font style="color:#24292E;">1-13</font>**

![](/assets/docs/比赛wp/porloo主线wp-1/img-186.jpeg)

<font style="color:#24292E;">分析简历.exe时发现了黑客的id：n0k4u</font>

![](/assets/docs/比赛wp/porloo主线wp-1/img-187.jpeg)

<font style="color:#24292E;">一般溯源可以通过github</font><sub><font style="color:#24292E;">中查看有无这个</font></sub><font style="color:#24292E;">id</font><sub><font style="color:#24292E;">的</font></sub><font style="color:#24292E;">github</font><sub><font style="color:#24292E;">主页</font></sub>

[<font style="color:#0366D6;">https://github.com/n0k4u</font>](https://github.com/n0k4u)

![](/assets/docs/比赛wp/porloo主线wp-1/img-188.jpeg)

![](/assets/docs/比赛wp/porloo主线wp-1/img-189.jpeg)

<font style="color:#24292E;">3834239649是个qq号(就算我当时找到了这个密语，我都不会觉得是qq号)</font>

![](/assets/docs/比赛wp/porloo主线wp-1/img-190.jpeg)

<font style="color:#24292E;">拿到溯源题目的flag</font>

![](/assets/docs/比赛wp/porloo主线wp-1/img-191.jpeg)

<font style="color:#24292E;">palu{loveyouibiejv}</font>

