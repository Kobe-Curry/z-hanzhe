## 查看系统信息

> 查看系统架构信息

通过`uname -m`命令查看系统架构信息，然后在下面的表格中找到对应的架构信息

```bash
$ uname -m
x86_64
```

| 输出            | 架构        |
| --------------- | ----------- |
| i386, i686      | i386        |
| x86_64          | amd64       |
| arm, arme       | arm_garbage |
| armv7l, armhf   | armv7       |
| aarch64, armv8l | arm64       |
| mips            | mips        |
| mips64          | mips64      |



## VIM相关笔记

> 快捷键

```shell
# 复制行：yy，复制5行：5yy
# 删除行：dd，删除5行：5dd
# 粘贴复制的行：p

# 回退到上一步：u
# 撤销上一步的回退：Ctrl + R
```

> 指令

```shell
# 显示/关闭行号
:set nu
:set nonu

# 开启/关闭自动缩进
:set paste
:set nopaste

# 替换内容，带%表示全文，不带%表示当前行
%s/aaa/bbb/g

# 查找内容，快捷键n为下一条，N为上一条
/aaa

# 关闭查找高亮
:nohlsearch
```



## 启用ROOT远程SSH

```shell
# 1. 使用VIM编辑ssdh配置文件
# 2. 找到Port属性并解开注释
# 3. 找到PermitRootLogin复制行，解开注释并修改为yes
$ vim /etc/ssh/sshd_config

# 重启sshd服务，就可以正常连接了
$ systemctl restart sshd
```



## 长命令起别名

```shell
# 编辑配置文件
$ vim ~/.bashrc

# 找到现有alias的位置，在结尾添加下面这段内容
alias rm='rm -i'
alias dk='docker'
alias dkc='docker-compose'

# 更新配置文件
$ source ~/.bashrc
```

