---
title: hexo建站学习
date: 2022-09-06 21:53:22
tags:
excerpt: 我是如何建立这个博客的
---
## 拥有一个linux服务器,带docker的
## 安装nodJs
### 安装nvm
``` shell
    # https://gitee.com/naozeizei/nvm-for-gitee
    # 国内安装nvm比较慢，我写了脚本能快速安装nodejs
    curl -o- https://gitee.com/naozeizei/nvm-for-gitee/raw/master/nvm-cn.sh | bash
    # 执行完毕后请重新建立一个terminal 
```
### 安装nodeJs
``` shell
    nvm install 12.0.0 # 官网要求6.0至少时12以上，装的时候以官网为准
```

## 安装hexo
1. 建议看官网http://hexo.io/完整教程
### 我的执行过程如下
``` shell 
# 构建目录
mkdir /hexo
cd /hexo 

# 初始化hexo
hexo init 

# npm 初始化
npm install 

# 生成静态文件，用于展示博客
hexo g
```

## 安装nginx
### 1. 拉取最新的nginx镜像
``` shell 
    docker pull nginx:latest
    docker images 
```
### 2. 启动一个临时容器，用来获取默认配置信息
``` shell 
# 建立nginx目录
mkdir /nginx
cd /nginx

#启动一个容器
 docker run -d --name nginx nginx
# 查看 容器 获取容器ID 或直接使用名字
docker container ls
# 在当前目录下创建目录：conf 
mkdir conf
# 拷贝容器内 Nginx 默认配置文件到本地当前目录下的 conf 目录（$PWD 当前全路径）
docker cp nginx:/etc/nginx/nginx.conf $PWD/conf
docker cp nginx:/etc/nginx/conf.d $PWD/conf

# 停止容器
docker container stop nginx
# 删除容器
docker container rm nginx

```

### 3. 启动docker
``` shell 

docker run -d -p 80:80  \
              -p 443:443  \
 --name nginxweb \
 -v /hexo/public:/usr/share/nginx/html \
 -v /nginx/conf/nginx.conf:/etc/nginx/nginx.conf \
 -v /nginx/conf/conf.d:/etc/nginx/conf.d \
 -v /nginx/logs:/var/log/nginx \
 nginx

```


### 4. 安装git 
``` shell
yum install git 

```

### 5. 安装主题
``` shell
# 进入主题目录
cd /hexo/themes/
# 下载fluid
git clone https://gitee.com/mirrors/hexo-theme-fluid.git
# 重命名
mv hexo-theme-fluid/ fluid

```
1. 修改 /hexo/_config.yml的 theme
    ``` yml
        theme: fluid
    ```
2. 添加备案
   ``` yml
	  beian:
	    enable: true
	    
   ```

### 6.  启动docker比较麻烦，还是docker-compose吧
``` shell 
cd /nginx
vim docker-compose.yml
```
``` yml 

version: '3.1'
services:
  nginx:
    image: nginx
    restart: always
    container_name: nginx
    environment:
      - TZ=Asia/Shanghai
    ports:
      - 80:80
      - 443:443
    volumes:
      # 我这使用了默认配置，要改的话再放开
      #- ./conf.d:/etc/nginx/conf.d 
      - ./logs:/var/log/nginx
      - /hexo/public:/usr/share/nginx/html

```

### 7. 增加评论功能
