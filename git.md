# 分支基本操作

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



# 解决方案

这里记录一些在使用GIT时容易遇到的情况的解决方案



## 找回误删除分支

```shell
# 以iso时间格式查看历史操作记录
git reflog show --date=iso

# 找到目标分支最后一条提交记录，复制ID后将提交迁出到新分支
git checkout -b 分支名 记录ID
```

