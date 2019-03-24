---
title: github ssh 
tags: 工具
date:  2019-01-07 16:00:00
---

> [Connecting to GitHub with SSH
](https://help.github.com/articles/connecting-to-github-with-ssh/)，官网教程很简单

### about ssh

```
# 使用 ssh 协议，每次可以免密码登录
Using the SSH protocol, you can connect and authenticate to remote servers and services. With SSH keys, you can connect to GitHub without supplying your username or password at each visit.
```

###checking for existing ssh keys

```shell
# exist ssh keys
$ ls ~/.ssh
id_rsa  id_rsa.pub  known_hosts
```

如果不存在，[生成 ssh key](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/)

```shell
# 生成ssh key
$ ssh-keygen
```

### Generating a new SSH key and adding it to the ssh-agent

#### Generating a new SSH

如果没有秘钥，生成秘钥

If you don't already have an SSH key, you must generate a new SSH key. If you're unsure whether you already have an SSH key, check for existing keys.


#### adding it to the ssh-agent

1、Start the ssh-agent in the background.

```shell
$eval "$(ssh-agent -s)"
```
2、Add your SSH private key to the ssh-agent. If you created your key with a different name, or if you are adding an existing key that has a different name, replace id_rsa in the command with the name of your private key file.

```shell
# id_rsa 为私钥文件
$ssh-add ~/.ssh/id_rsa
```

3、check
```shell
$ ssh-add -l 
```

### Add the SSH key to your GitHub account.

```shell
# 将 public key 复制到剪贴板
$ xclip -sel clip < ~/.ssh/id_rsa.pub 
```
登录 github 

settings - > SSH and GPG keys -> new SSH key 

###  Testing your SSH connection

https://help.github.com/articles/testing-your-ssh-connection/

```shell
$ ssh -T git@github.com
Hi yangyang5214! You've successfully authenticated, but GitHub does not provide shell access.
```
### 使用

```shell
# 没问题就会正常clone , 如果 是 Permission denied，则check  ssh-add -l 是否有值
$git clone git@github.com:ChestnutHeng/Wudao-dict.git
```