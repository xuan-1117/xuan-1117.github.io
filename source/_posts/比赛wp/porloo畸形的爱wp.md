---
title: "porloo畸形的爱wp"
date: 2025-12-10 08:52:47
categories: "比赛wp"
tags:
  - "比赛wp"
cover: /img/backgrounds/bg-20.png
top_img: /img/backgrounds/bg-20.png
disableNunjucks: true
---

# 应急响应1-1


![](/assets/docs/比赛wp/porloo畸形的爱wp/img-001.jpeg)

<font style="color:#24292E;">一般应急，我的思路是先看入口点(一般被攻击的情况下，就是从互联网端打进来的，所以着重看</font>

<font style="color:#24292E;">一下哪些机器是公网可以访问到的，一般这种机器都是突破口，后期的内网渗透也是拿这台机器</font>

<font style="color:#24292E;">当作跳板机)。但是整个做题过程中发现， 好不要这样，看了其他大佬的wp，发现很多题目的</font>

<font style="color:#24292E;">关键是在其他几个机器中找到的，所以接收下这个教训，不要死磕一两个机器，如果没有思路，</font>

<font style="color:#24292E;">一定要看完整个网络环境下的其他机器。</font>

<font style="color:#24292E;">按照题目给的拓扑来看：</font>

![](/assets/docs/比赛wp/porloo畸形的爱wp/img-002.jpeg)

<font style="color:#24292E;">整个网络拓扑图的入口点是在web server服务器</font>

<font style="color:#24292E;">使用netstat -anplt查看web server服务器对外开放了几个端口，web端口一般都是突破口</font>

<font style="color:#24292E;">本题webserver服务器开着80端口，跑着nginx服务器，所以找攻击者的攻击ip，一般是要看nginx 日志有无攻击记录</font>

![](/assets/docs/比赛wp/porloo畸形的爱wp/img-003.jpeg)

<font style="color:#24292E;">一般ubuntu服务器上的日志存放位置为/var/log中，我们看到了nginx目录，所以进入查看nginx 目录中有什么日志文件</font>

![](/assets/docs/比赛wp/porloo畸形的爱wp/img-004.jpeg)

<font style="color:#24292E;">访问日志是access.log，报错日志是error.log后面那个.1就是代表之前存的日志</font>

<font style="color:#24292E;">查看道这个ip192.168.31.240访问的这几个php文件就不正常，所以是攻击地址</font>

![](/assets/docs/比赛wp/porloo畸形的爱wp/img-005.jpeg)

<font style="color:#24292E;">palu{192.168.31.240}</font>

# 应急响应1-2


![](/assets/docs/比赛wp/porloo畸形的爱wp/img-006.jpeg)

<font style="color:#24292E;">这个题，其实可以套路下就能出答案。</font>

<font style="color:#24292E;">还是顺着正常流程去讲</font>

<font style="color:#24292E;">一般我们在看webserver这个互联网入口的web服务器，我们要知道这台服务器上开启了哪些web 服务，要都访问一下，做到心中有数，因为只有知道了具体的web服务，你才能在自己心中设想</font>

<font style="color:#24292E;">出，哪部分服务会有问题，可能出现什么攻击思路</font>

<font style="color:#24292E;">而且有时候，其实看一下历史记录往往会有一些意想不到的发现(你想呀，通过history，你也能知</font>

<font style="color:#24292E;">道运维部署了什么环境，建议拿到提目前先把root用户跟当前用户的history记录给down下来保存好，便分析边应急) 通过历史记录可以看到，运维的环境都是用docker部署的</font>

<font style="color:#24292E;">使用docker ps可以查看当前docker开启了哪些容器，分别占用了哪些端口</font>

![](/assets/docs/比赛wp/porloo畸形的爱wp/img-007.jpeg)

<font style="color:#24292E;">可以使用命令进入指定的容器名称 docker exec -it </font><sub><font style="color:#24292E;">容器</font></sub><font style="color:#24292E;">id /bin/sh</font>

![](/assets/docs/比赛wp/porloo畸形的爱wp/img-008.jpeg)

<font style="color:#24292E;">nc反连地址：</font>

<font style="color:#24292E;">palu{192.168.31.11}</font>



**<font style="color:#24292E;">应急响应</font>**

**<font style="color:#24292E;">1-3</font>**

![](/assets/docs/比赛wp/porloo畸形的爱wp/img-009.jpeg)

	<font style="color:#24292E;">docker	phpmyadmin</font>

<font style="color:#24292E;">题目在问暴力破解，看了 开启的几个环境，只有 容易有暴力破解的攻击场 phpmyadmin 景，但是进入 </font><sub><font style="color:#24292E;">容器也没找到日志</font></sub>

<font style="color:#24292E;">Docker 容器默认不会将日志写入磁盘文件（除非你特别配置了挂载）。它会把日志写到容器标准输出</font>

<font style="color:#24292E;">显示启动信息、访问日志、错误信息等</font>

<font style="color:#24292E;">docker logs phpmyadmin</font>

![](/assets/docs/比赛wp/porloo畸形的爱wp/img-010.jpeg)

<font style="color:#24292E;">短时间内，大量访问统一url</font><sub><font style="color:#24292E;">，还是</font></sub><font style="color:#24292E;">post</font><sub><font style="color:#24292E;">请求方式，请求的</font></sub><font style="color:#24292E;">url</font><sub><font style="color:#24292E;">也是</font></sub><font style="color:#24292E;">phpmyadmin</font><sub><font style="color:#24292E;">的登录</font></sub><font style="color:#24292E;">url</font>

<font style="color:#24292E;">palu{2025:03:05:58}</font>



**<font style="color:#24292E;">应急响应</font>**

**<font style="color:#24292E;">1-4</font>**

![](/assets/docs/比赛wp/porloo畸形的爱wp/img-011.jpeg)

<font style="color:#24292E;">此时需要用到pc机器了(做题卡在某个机器上一旦没思路的时候，一定要看其他机器，我看了web 服务后总感觉会藏flag，找了好长的时间没找到，也不想看其他机器了) win10 pc2</font><sub><font style="color:#24292E;">中直接用</font></sub><font style="color:#24292E;">everything</font><sub><font style="color:#24292E;">可以找到</font></sub><font style="color:#24292E;">flag1</font>

![](/assets/docs/比赛wp/porloo畸形的爱wp/img-012.jpeg)

<font style="color:#24292E;">palu{pc3_zgsfqwerlkssaw}</font>

![](/assets/docs/比赛wp/porloo畸形的爱wp/img-013.jpeg)

<font style="color:#24292E;">或者一般在windows应急时，大家比较关注任务计划，也能看到flag信息</font>

![](/assets/docs/比赛wp/porloo畸形的爱wp/img-014.jpeg)

# 应急响应1-5


![](/assets/docs/比赛wp/porloo畸形的爱wp/img-015.jpeg)

<font style="color:#24292E;">还是flag1</font><sub><font style="color:#24292E;">这个文件中</font></sub>

![](/assets/docs/比赛wp/porloo畸形的爱wp/img-016.jpeg)

## 含有a.bat这个文件，打开这个文件发现flag2
![](/assets/docs/比赛wp/porloo畸形的爱wp/img-017.jpeg)

<font style="color:#24292E;">flag2palu{nizhidaowoyouduoainima}</font>

# 应急响应1-6


![](/assets/docs/比赛wp/porloo畸形的爱wp/img-018.jpeg)

<font style="color:#24292E;">flag3</font><sub><font style="color:#24292E;">在</font></sub><font style="color:#24292E;">mysql</font><sub><font style="color:#24292E;">数据库中</font></sub>

![](/assets/docs/比赛wp/porloo畸形的爱wp/img-019.jpeg)

<font style="color:#24292E;">看到大佬的wp，真的细，不服不行。我当时就猜测肯定有个flag在数据库，这里我也看到了，就</font>

<font style="color:#24292E;">是简单的一扫而过</font>

![](/assets/docs/比赛wp/porloo畸形的爱wp/img-020.jpeg)

<font style="color:#24292E;">当时就感觉这个Mr.chen有问题，因为它的订单时间就跟攻击时间太吻合，一时没想起来这个点</font>

<font style="color:#24292E;">怎么利用</font>

![](/assets/docs/比赛wp/porloo畸形的爱wp/img-021.jpeg)

<font style="color:#24292E;">base64</font><sub><font style="color:#24292E;">解码后，发现</font></sub><font style="color:#24292E;">flag3</font>

<font style="color:#24292E;">palu{sqlaabbccsbwindows}</font>

<font style="color:#24292E;">其实，你在了解系统的时候，把订单都查查也能看到flag3</font>

![](/assets/docs/比赛wp/porloo畸形的爱wp/img-022.jpeg)

# 应急响应1-7


![](/assets/docs/比赛wp/porloo畸形的爱wp/img-023.jpeg)

<font style="color:#24292E;">如果细心翻一下回收站是可以看到钓鱼文件的</font>

![](/assets/docs/比赛wp/porloo畸形的爱wp/img-024.jpeg)

<font style="color:#24292E;">在实战中，其实都是hr跟客服容易被钓鱼，一般钓鱼文件要么是一些通知、要么就是简历等符合</font>

<font style="color:#24292E;">工作特性的文件</font>

![](/assets/docs/比赛wp/porloo畸形的爱wp/img-025.jpeg)

<font style="color:#24292E;">简历.zip被删了，在回收站中，还原即可 win计算文件md5的命令：</font>

<font style="color:#24292E;">certutil -hashfile "</font><sub><font style="color:#24292E;">文件路径</font></sub><font style="color:#24292E;">" md5</font>

![](/assets/docs/比赛wp/porloo畸形的爱wp/img-026.jpeg)

<font style="color:#24292E;">题目要求是大写，所以换成大写即可</font>

<font style="color:#24292E;">palu{2977CDAB8F3EE5EFDDAE61AD9F6CF203}</font>

# 应急响应1-8


![](/assets/docs/比赛wp/porloo畸形的爱wp/img-027.jpeg)

<font style="color:#24292E;">之前在web服务器中，看到nginx日志中，黑客ip访问了a.php，所以这个文件很敏感</font>

<font style="color:#24292E;">还有一种思路就是，黑客一般会在web服务器中传webshell(题目讲了)，我们分析web服务器各个端口开启的服务发现，使用的语言是php语言，所以我们可以查看下5月1号-5月2号这个期间web 服务器上有无修改过的php文件(应急响应一定要对时间敏感，有时候可以把时间当作筛查条件进行筛查)</font>

<font style="color:#24292E;">查找5月1日和5月2日修改过的PHP文件：</font>

<font style="color:#24292E;">find / -type f -name "*.php" -newermt "2025-05-01" ! -newermt "2025-05-03" 2>/dev/null</font>

![](/assets/docs/比赛wp/porloo畸形的爱wp/img-028.jpeg)

<font style="color:#24292E;">执行后可以发现这几个文件</font>

![](/assets/docs/比赛wp/porloo畸形的爱wp/img-029.jpeg)

<font style="color:#24292E;">一下可以查看两个webshell的木马 palu{hack}</font>

# 应急响应1-9


![](/assets/docs/比赛wp/porloo畸形的爱wp/img-030.jpeg)

<font style="color:#24292E;">详细解看有的师傅放出来的wp</font>

[<font style="color:#0366D6;">https://blog.csdn.net/qq_30817059/article/details/148061612?</font>](https://blog.csdn.net/qq_30817059/article/details/148061612?sharetype=blogdetail&shareId=148061612&sharerefer=APP&sharesource=qq_30817059&sharefrom=qq)

[<font style="color:#0366D6;">sharetype=blogdetail&shareId=148061612&sharerefer=APP&sharesource=qq_30817059&share from=qq</font>](https://blog.csdn.net/qq_30817059/article/details/148061612?sharetype=blogdetail&shareId=148061612&sharerefer=APP&sharesource=qq_30817059&sharefrom=qq)



**<font style="color:#24292E;">应急响应</font>**

**<font style="color:#24292E;">1-10</font>**

![](/assets/docs/比赛wp/porloo畸形的爱wp/img-031.jpeg)

<font style="color:#24292E;">palu{00232}</font>



**<font style="color:#24292E;">应急响应</font>**

**<font style="color:#24292E;">1-11</font>**

![](/assets/docs/比赛wp/porloo畸形的爱wp/img-032.jpeg)

<font style="color:#24292E;">使用D</font><sub><font style="color:#24292E;">盾发现了隐藏账户</font></sub><font style="color:#24292E;">system$</font>

![](/assets/docs/比赛wp/porloo畸形的爱wp/img-033.jpeg)

<font style="color:#24292E;">使用mimikatz</font><sub><font style="color:#24292E;">跑出</font></sub><font style="color:#24292E;">hash</font><sub><font style="color:#24292E;">来</font></sub><font style="color:#24292E;">提取 SAM & SYSTEM 文件手动离线分析，从系统中导出 </font><font style="color:#24292E;">SAM</font><font style="color:#24292E;"> 和 </font><font style="color:#24292E;">SYSTEM</font><font style="color:#24292E;"> 文件，然后用 mimikatz</font>

<font style="color:#24292E;">离线读取：导出命令(cmd管理员权限启动)：</font>

<font style="color:#24292E;">reg save hklm\sam sam.save </font>

<font style="color:#24292E;">reg save hklm\system system.save</font>

<font style="color:#24292E;">然后在 mimikatz </font><sub><font style="color:#24292E;">中执行：</font></sub>

<font style="color:#24292E;">privilege::debug lsadump::sam /sam:sam.save /system:system.save</font>

![](/assets/docs/比赛wp/porloo畸形的爱wp/img-034.jpeg)

<font style="color:#24292E;">Hash NTLM: dbae99beb48fd9132e1cf77f4c746979</font>

## 去md5网站进行查询
![](/assets/docs/比赛wp/porloo畸形的爱wp/img-035.jpeg)

<font style="color:#24292E;">据说解密出来的值为： wmx_love 反过来加密可以验证，对的上</font>

![](/assets/docs/比赛wp/porloo畸形的爱wp/img-036.jpeg)

<font style="color:#24292E;">palu{wmx_love}</font>

# 应急响应1-12


![](/assets/docs/比赛wp/porloo畸形的爱wp/img-037.jpeg)

<font style="color:#24292E;">通过逆向手段拿到了黑客的id</font>

<font style="color:#24292E;">这个黑客id在github上有仓库，可以泄露出邮箱来：</font>

<font style="color:#24292E;">使用api接口可以进行查询：通过获取目标 Github 用户名，替换下面链接中的</font><font style="color:#24292E;"><name></font><font style="color:#24292E;"> 进行查询：</font>

[<font style="color:#0366D6;">https://api.github.com/users/</font>](https://api.github.com/users/)<font style="color:#24292E;">/events/public 访问：</font>[<font style="color:#24292E;"> </font>](https://api.github.com/users/n0k4u/events/public)[<font style="color:#0366D6;">https://api.github.com/users/n0k4u/events/public</font>](https://api.github.com/users/n0k4u/events/public)

![](/assets/docs/比赛wp/porloo畸形的爱wp/img-038.jpeg)

<font style="color:#24292E;">参考文章：</font>

[<font style="color:#0366D6;">https://blog.csdn.net/fishfishfishman/article/details/141320003</font>](https://blog.csdn.net/fishfishfishman/article/details/141320003)

<font style="color:#24292E;">邮箱号：</font>

<font style="color:#0366D6;">n0k4u@outlook.com </font><font style="color:#24292E;">palu{n0k4u@outlook.com}</font>



**<font style="color:#24292E;">应急响应</font>**

**<font style="color:#24292E;">1-13</font>**

![](/assets/docs/比赛wp/porloo畸形的爱wp/img-039.jpeg)

<font style="color:#24292E;">分析简历.exe时发现了黑客的id：n0k4u</font>

![](/assets/docs/比赛wp/porloo畸形的爱wp/img-040.jpeg)

<font style="color:#24292E;">一般溯源可以通过github</font><sub><font style="color:#24292E;">中查看有无这个</font></sub><font style="color:#24292E;">id</font><sub><font style="color:#24292E;">的</font></sub><font style="color:#24292E;">github</font><sub><font style="color:#24292E;">主页</font></sub>

[<font style="color:#0366D6;">https://github.com/n0k4u</font>](https://github.com/n0k4u)

![](/assets/docs/比赛wp/porloo畸形的爱wp/img-041.jpeg)

![](/assets/docs/比赛wp/porloo畸形的爱wp/img-042.jpeg)

<font style="color:#24292E;">3834239649是个qq号(就算我当时找到了这个密语，我都不会觉得是qq号)</font>

![](/assets/docs/比赛wp/porloo畸形的爱wp/img-043.jpeg)

<font style="color:#24292E;">拿到溯源题目的flag</font>

![](/assets/docs/比赛wp/porloo畸形的爱wp/img-044.jpeg)

<font style="color:#24292E;">palu{loveyouibiejv}</font>

