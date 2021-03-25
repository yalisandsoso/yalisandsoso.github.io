---
bg: "tools.jpg"
layout: post
title:  Jupyter Server
crawlertitle: Jupyter 
summary: Python
date:   2021-03-25
categories: python
tags: ['Niu 03']
author: hekun
bg: "african-penguins.jpg"
---

Jupyter是python开发的利器，为了远程登录搞事情，决定在腾讯云上部署一个Jupyter server跑跑代码。注:CentOS 7.6,Jupyter,python3.7

## code

更新yum

```
yum clean all
yum update
```

mwget 提速

```
wget http://jaist.dl.sourceforge.net/project/kmphpfm/mwget/0.1/mwget_0.1.0.orig.tar.bz2

tar -xjvf mwget_0.1.0.orig.tar.bz2

cd mwget_0.1.0.orig

yum install intltool
./configure
make
make install
```

install python3.7

```bash
whereis python
cd /bin
ll python*

yum install zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gcc make libffi-devel

#运行这个命令添加epel扩展源 
yum -y install epel-release 
#安装pip 
yum install python-pip

pip install wget
pip install --upgrade pip

wget https://www.python.org/ftp/python/3.7.0/Python-3.7.0.tgz
mwget https://www.python.org/ftp/python/3.7.0/Python-3.7.0.tgz

cd Python-3.7.0/

./configure prefix=/usr/local/python3
make && make install

cd /usr/local/python3

#添加python3的软链接 
ln -s /usr/local/python3/bin/python3.7 /usr/bin/python3.7 
#添加 pip3 的软链接 
ln -s /usr/local/python3/bin/pip3.7 /usr/bin/pip3.7
#测试是否安装成功了 
python3.7 -V

vi /usr/bin/yum
# 将第一行 #!/usr/bin/python 改为 #!/usr/bin/python2.7

# 因为yum源使用python2，前面替换为python3后，会导致其无法正常工作

```

install jupyter

```bash
pip3.7 install jupyter
jupyter notebook --generate-config
# Writing default config to: #/root/.jupyter/jupyter_notebook_config.py


python3.7
>>from notebook.auth import passwd

>>passwd()
# 123456 123456
# 复制密钥

vim ~/.jupyter/jupyter_notebook_config.py
c.NotebookApp.ip='*' 

c.NotebookApp.password = u'# 刚才复制的那个密文'

c.NotebookApp.open_browser = False  # 禁止自动打开浏览器

c.NotebookApp.port=8888   #随便指定一个端口， 服务器开启8888
```

开启jupyter

```c
jupyter notebook --allow-root
```

测试

```
serverip:8888
# passwd
# 123456
```


## support

https://jupyter.org/
