# Heroku介绍

一个云端的软件运行平台，我们可以把我们的软件部署在云端，在公网进行运行。
只要我们的软件在本地运行没有问题，我们可以很方便的在云端使用相同的环境进行部署运行。

免费用户，需要使用24小时，停止6小时。
免费用户，your app is running on a single web dyno, 也就是运行在单个web服务上，

界面如下：

![image](https://raw.githubusercontent.com/zhusheng/blog/master/heroku/01.png)

## 环境准备

[官方文档](https://devcenter.heroku.com/articles/getting-started-with-python)

[官方|开发者中心](https://devcenter.heroku.com/)

[官方|下载和安装](https://devcenter.heroku.com/articles/heroku-cli#download-and-install)

我使用brew安装失败了， 所以我选择了客户端安装。

[官方|卸载](https://devcenter.heroku.com/articles/heroku-cli#uninstalling-the-heroku-cli)

[官方| APPs](https://dashboard.heroku.com/apps)

我的账号：zhusheng185@gmail.com，密码：浏览器建议的强密码，不用记

## Heroku入门教程（Python + Flask）

这里，我以一个示例来说明Heroku的作用。[参考YouTube Videos](https://www.youtube.com/watch?v=QTOkqzCTGxw)

![image](https://raw.githubusercontent.com/zhusheng/blog/master/heroku/02.png)

### 1.Write an app

第一步：新建隔离环境

```bash
mkvirtualenv -p python3.6 hello_heroku
```

第二步：安装软件

```bash
pip install Flask gunicorn
```

第三步：新建项目

- 新建工作目录：/Users/zhusheng/WorkSpace/Django/heroku_tutorial
- 新建文件：hello.py,代码如下：

```python
import os
from flask import Flask

app = Flask(__name__)

@app.route('/')
def hello():
	return 'Hello Heroku!'
```

### 2. Run locally

启动项目

```bash
gunicorn hello:app
```

如下图所示：

![image](https://raw.githubusercontent.com/zhusheng/blog/master/heroku/03.png)

浏览器访问如下图所示：

![image](https://raw.githubusercontent.com/zhusheng/blog/master/heroku/04.png)

根据Heroku要求，我们需要生成我的requirements.txt，执行如下指令，将当前项目依赖的环境生成一个requirements.txt文件。Heroku会根据该文件在云端创建相同的环境。

```bash
pip freeze > requirements.txt 
```

如下图所示：

![image](https://raw.githubusercontent.com/zhusheng/blog/master/heroku/05.png)

根据Heroku要求，新建Procfile文件，内容如下：

```
web: gunicorn hello:app --log-file -
```

### 3. Version in git

新建.gitignore文件，内容如下：

```bash
venv
.pyc
```

在当前目录下生成仓库

```bash
git init 
git add .
git commit -m "initial commit"
```

### 4.Create an application

新建heroku项目

```bash
heroku create hello-heroku-2019
```

效果图如下：

![image](https://raw.githubusercontent.com/zhusheng/blog/master/heroku/06.png)

内容如下：

```bash
https://hello-heroku-2019.herokuapp.com/ 
https://git.heroku.com/hello-heroku-2019.git
```

第一个链接是我们项目在公网的地址，这样在本地运行的项目可以在公网进行运行;
第二个链接是我们项目在heroku git仓库的地址，方便进行软件的版本管理，它使用的是git进行版本管理。

访问浏览器效果如下：

![image](https://raw.githubusercontent.com/zhusheng/blog/master/heroku/07.png)

### 5. Deploy

我们将项目推送到远程，他会在heroku中安装软件依赖的环境，也就是requirements.txt中的软件。
这样能保证我的代码无论何时都是运行在这个环境下，不会随着软件更新而发生无法运行的问题。
也不用担心版本更新的问题，如果我们的软件需要进行迭代更新，我们只需要再发布一个版本即可。

发布指令：

```bash
git push heroku master
```

效果图如下：

![image](https://raw.githubusercontent.com/zhusheng/blog/master/heroku/08.png)

我们在Heroku Cloud看一下我的项目，如下所示：

![image](https://raw.githubusercontent.com/zhusheng/blog/master/heroku/09.png)

我们重新访问浏览器，效果如下：

![image](https://raw.githubusercontent.com/zhusheng/blog/master/heroku/10.png)

我们看到我的本地项目在Cloud中运行成功了，It is so cool!

### 6.Logging

查看项目运行日志

```bash
heroku logs -t 
```

效果图如下所示：

![image](https://raw.githubusercontent.com/zhusheng/blog/master/heroku/11.png)

### 7.Add-ons

现在，我对我的项目进行迭代更新，假设迭代后的代码如下：

```python
import os
from flask import Flask
import logging

app = Flask(__name__)
logging.basicConfig(level=logging.DEBUG)

@app.route('/')
def hello():
	logging.debug("saying hello heroku!")
	return 'Hello Heroku!, It\'s so cool!'
```

更新代码到Heroku仓库

```bash
git add .
git commit -m "added logging"
git push heroku master
```

我们重新访问一下浏览器：

![image](https://raw.githubusercontent.com/zhusheng/blog/master/heroku/12.png)

### 8.Configuration variables

修改我的hello.py文件，修改后如下所示：

```python
import os
from flask import Flask
import logging

app = Flask(__name__)
logging.basicConfig(level=logging.DEBUG)

@app.route('/')
def hello():
	logging.debug("saying hello heroku!")
	name = os.environ.get('NAME','DreamForceXXXX')
	return 'Hello %s!' % name
```

主要改了2行代码，这里我希望我的代码能接收到我通过heroku传递的参数，动态的显示在浏览器上。

我修改了我的代码，首先，我需要传递到远程heroku git仓库。

```bash

git add .
git commit -m "added heroku environ"
git push heroku master
```

我们访问浏览器看一下效果：

![image](https://raw.githubusercontent.com/zhusheng/blog/master/heroku/13.png)

通过heroku设置参数和获取参数

```bash
# 设置参数
heroku config:set NAME=Snkey
heroku config:set NAME=Hello Heroku

# 获取参数
heroku config:get NAME
```

我们先设置参数，然后刷新浏览器看效果，执行效果如下：

![image](https://raw.githubusercontent.com/zhusheng/blog/master/heroku/14.png)

我们注意到这里有一个“v7”，它是一个版本记录我们访问浏览器查看一下效果：

![image](https://raw.githubusercontent.com/zhusheng/blog/master/heroku/15.png)

如果我们多次设置，我们是可以通过这个“v7”\”v8” and so on进行回滚操作的，回滚指令如下：

```bash
heroku rollback v7
```

### 9.Releases

Heroku是可以对我们提交的代码修改保存不同的版本，我们可以通过如下的指令查看所有的版本：

![image](https://raw.githubusercontent.com/zhusheng/blog/master/heroku/16.png)

我们可以在不同的版本之间进行回滚，通过“v7”\”v6” and so on。我们通过下面的指令进行回滚操作：

```bash
heroku rollback v7
```

查看heroku当前运行的app process，查看我当前的apps运行情况，执行如下所示：

![image](https://raw.githubusercontent.com/zhusheng/blog/master/heroku/17.png)

### 10.Scale

Heroku提供了Scale功能，我们可以手动通过指令的形式去修改APP的运行规模，当然也是可以通过Heroku Dashboard Metrics进行可视化修改。

Scale示例如下：

```bash
heroku ps:scale web=4
```

默认，免费账户只能设置web=1，更多的需要付费购买。
