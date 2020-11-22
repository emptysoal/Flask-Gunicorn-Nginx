# Nginx + gunicorn + gevent + flask

## 编写 flask 脚本

- demo.py (最后两行可不加)

```python
# -*- coding:utf-8 -*-

from flask import Flask
import time


app = Flask(__name__)

@app.route('/')
def index():
    time.sleep(0.01)  # 模拟程序阻塞
    return 'hello world!'


if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)

```

## gunicorn 启动python脚本

```bash
$ gunicorn --workers 4 --bind 0.0.0.0:5000 --daemon --worker-class gevent demo:app
```

或：

```bash
$ gunicorn -w 4 -b 0.0.0.0:5000 -D -k gevent demo:app
```

## 配置nginx反向代理

- 进入nginx配置文件

```bash
$ vim /etc/nginx/conf.d/default.conf
```

或：

```bash
$ vim /etc/nginx/sites-enabled/default.conf
```

- 写下如下内容：

```
server {
    listen       80;
    listen  [::]:80;
    server_name  localhost;

    #charset koi8-r;
    #access_log  /var/log/nginx/host.access.log  main;

    location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm;
        proxy_pass http://0.0.0.0:5000;
    }
}
```

## 启动nginx

```bash
$ nginx
```

或：

```bash
$ /usr/sbin/nginx
```

### 备注

停止nginx服务：`/usr/sbin/nginx -s stop`，重启nginx服务：`/usr/sbin/nginx -s reload`

## 测试

- 浏览器中输入`127.0.0.1`

- 显示出 “hello world!”，则成功。

