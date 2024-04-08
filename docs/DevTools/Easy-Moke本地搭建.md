# Easy-Moke本地搭建

## 1、安装Node.js 8.9.1

* 安装nvm 管理Node.js版本

  配置环境变量——.bash_profile

  ```bash
  #Setting PATH for NVM
  export NVM_DIR="$HOME/.nvm"
    [ -s "/opt/homebrew/opt/nvm/nvm.sh" ] && \. "/opt/homebrew/opt/nvm/nvm.sh"  # This loads nvm
    [ -s "/opt/homebrew/opt/nvm/etc/bash_completion.d/nvm" ] && \. "/opt/homebrew/opt/nvm/etc/bash_completion.d/nvm"  # This loads nvm bash_completion
  ```

  ```bash
  brew install nvm
  nvm list #查看node版本
  nvm use system #使用16.13.0 这个是我用npm下载的
  nvm use default #使用8.9.1
  nvm install 版本号 如8.9.1
  ```

## 2、安装redis

```bash
brew install redis 
redis-cli #客户端
redis-server #服务端
```

修改配置文件/opt/homebrew/etc/redis.conf

```bash
daemonize no#yes 后台运行
```

## 3、安装Mongodb

* brew 中查不到Mongodb，该用[官网安装]( https://fastdl.mongodb.org/osx/mongodb-osx-ssl-x86_64-4.0.4.tgz ) 

* 配置环境变量

```bash
#Setting PATH for MongOdb
export PATH=Mongodb安装路径名/bin:${PATH}
```

* 启动Mongodb

  在安装目录下，新建/data/db 目录

  ```bash
  切换目录到安装目录bin目录下：
  终端1:./mongod --dbpath /Users/zhangqian20/Project/Moke/mongodb-osx-x86_64-4.0.4/data/db
  
  终端2:./mongo
  ```

若没有这个操作会宝连接失败的错误

## 4、安装easy-mock

从官网上下载[easy-make](https://github.com/easy-mock/easy-mock.git)

```bash
cd easy-mock
npm install #下载依赖
npm run dev  #运行程序
```

## 5、安装nginx(暂时没有用到，因为不是纯前端项目，node就是一个Web服务器)

```bash
brew install nginx
brew info nginx #查看nginx配置信息
sudo nginx -s reload #重启nginx
```

在**nginx.conf**文件中配置网站信息

```bash
    server{
        listen  5188; #自定义端口
        server_name localhost; 
        location / {
            root /Users/zhangqian20/Project/Moke/easy-mock/dist;#网站配置地址
            index index.html index.htm;
        }
    }
```



## 6、错误码解决方法

* 错误：events.js:183 throw er; // Unhandled 'error' event—解决办法

```bash
lsof -i:7300 #查找端口号的线程
kill PI(7300的线程ID) #杀死线程
```

## 7、查看数据库是否启动

```bash
lsof -i:6379   #redis
pgrep mongo -l #Mongodb
```

# EASY-MOKE使用语法

1、[基础语法](https://github.com/nuysoft/Mock/wiki/Syntax-Specification)



