## 分支操作

```shell
# 新建分支
git checkout -b 分支名

# 从远程分支更新代码
git pull origin 分支名

# 切换分支
git checkout 分支名

# 合并分支
git merge 分支名
```



## 远程仓库

```shell
# 查看远程仓库列表 (fetch=拉取代码，push提交代码)
git remote -v

# 添加远程仓库
git remote add [名称] [地址]

# 修改指定名称的远程仓库的URL地址，[操作]为可选项 --push --fetch
git remote set-url [操作] [名称] [地址]

# 删除远程仓库
git remote rm [名称]

# 远程仓库重命名
git remote rename [旧名称] [新名称]
```



## 解决方案

这里记录一些在使用GIT时容易遇到的情况的解决方案



### 配置代理地址

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



### 找回误删除分支

```shell
# 以iso时间格式查看历史操作记录
git reflog show --date=iso

# 找到目标分支最后一条提交记录，复制ID后将提交迁出到新分支
git checkout -b 新分支名 记录ID
```

