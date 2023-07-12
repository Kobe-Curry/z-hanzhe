**一些未分类的笔记会记在这个位置**

## 结束指定端口进程

> Windows

```shell
# 找到占用该端口号的程序的PID
$ netstat -ano | findstr 端口号

# 然后根据PID结束应用程序
$ taskkill -pid 程序的PID -f

# 根据进程名称终止进程（例如Windows的Nginx比较顽固，无法通过PID彻底结束，需要使用进程名）
$ taskkill /F /IM nginx.exe
```
> Linux

```shell
# 找到占用该端口号的程序的PID
$ netstat -apn | grep 端口号

# 然后根据PID结束应用程序
$ kill -9 程序的PID

# 根据进程名称查询PID
$ ps -ef | grep 进程名 | grep -v grep
```



## 代理的妙用

> NPM设置代理

```shell
# 设置代理
npm config set proxy=http://127.0.0.1:7890
npm config set https-proxy=http://127.0.0.1:7890

# 删除代理
npm config delete proxy
npm config delete https-proxy

# 查看代理设置情况
npm config get proxy
npm config get https-proxy
```



> GIT设置代理

```shell
# GIT全局代理
git config --global http.proxy http://127.0.0.1:7890
git config --global https.proxy http://127.0.0.1:7890

# 查看代理配置
git config --global --get http.proxy
git config --global --get https.proxy

# 取消代理
git config --global --unset http.proxy
git config --global --unset https.proxy

# 只对Github代理
git config --global http.https://github.com.proxy http://127.0.0.1:7890
git config --global http.https://github.com.proxy socks5://127.0.0.1:7890

# 取消Github代理
git config --global --unset http.https://github.com.proxy
```

