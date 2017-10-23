---
title: git 问题汇总
date: 2017-10-22 21:33:21
tags:
- git
categories:
- 笔记
- git
---

- ### github/gitlab 多账户下设置ssh key
1. 使用命令：ssh-keygen -t rsa -C "注册git的邮箱"，命名时设置不一致的名字即可（id_rsa_*）
2. 将对应的id_rsa_*.pub文件内容放到相应的git账号的SSH Keys中
3. 配置config文件：在 .ssh/目录下新建config文件：==touch config== ，内容例子
```
#gitlab
Host gitlab.*.com
    HostName gitlab.*.com
    IdentityFile ~/.ssh/id_rsa_*
    
#github
Host github.com
    HostName github.com
    IdentityFile ~/.ssh/id_rsa_*
```
> Host 和 HostName 教程说Host可以随便起，但是亲测不行，需要保持一致，原因不明

---
- ### fatal: Unable to create 'XXX/.git/index.lock’: File exists

> 解决方法：在.git同级目录，执行rm -f .git/index.lock （或者rm -f git/index.lock） 删除后可提交

---
- ### git pull 失败 ,提示：fatal: refusing to merge unrelated histories

解决：
```
git pull origin master --allow-unrelated-histories
```
---
- ### HEAD detached at xxxx

> 场景：使用git checkout [tagName] 切换标签后出现此情况

> 问题说明: 此时HEAD处于游离状态(detached HEAD), 此时进行commit操作不会提交到任何分支上

> 解决: git branch 查看分支, git checkout xxx 回到需要的分支

---
- ### 一个仓库包含另一个仓库如何处理

> 子模块: 一个仓库包含另一个仓库 [http://www.jianshu.com/p/491609b1c426](http://www.jianshu.com/p/491609b1c426)

1. 在现有的仓库中加入子模块

```bash
git submodule add <>
```

查看子模块信息

```bash
git diff --cached --submodule
```

2. 克隆包含子模块的仓库

直接克隆后默认有子模块目录， 但是是空的, 需要初始化子模块：

```bash
git submodule init

# 或者克隆时加入参数， 可达同样效果
git clone --recursive <xxx>
```

更新子模块(需要进入子项目目录)

```bash
cd <子模块目录>
git pull

# 或

git submodule update --remote <>

# 这里默认更新master分支，如果更新其他分支
$ git config -f .gitmodules submodule.DbConnector.branch stable
$ git submodule update --remote
$ git merge origin/master
```

3. 修改子模块

切换到要修改代码的子模块分支

```bash
$ cd DbConnector
$ git checkout stable
```

拉取服务器代码，并且合并到本地分支stable

```bash
$ git submodule update --remote --merge
```

推送本次修改

```bash
$ git push
```

4. 主仓库推送相关注意点

主仓库推送时，确保子模块的修改已经推送，下面命令会检查子模块修改的内容是否推送，如果没有，主仓库推送也会失败

```
$ git push --recurse-submodules=check

$ git push --recurse-submodules=on-demand # Git会检查到子模块没有推送，会自动推送子模块，然后再推送主模块
```

5. 主仓库包含多个子模块

foreach可以遍历所有子模块，下面是把子模块存储起来

```
$ git submodule foreach 'git stash'
```

移动刚刚储藏的子模块到新分支，然后开始新的bug修复等开发

```bash
$ git submodule foreach 'git checkout -b featureA'
```

显示主项目和子模块的所有改动

```
$ git diff; git submodule foreach 'git diff'
```

主仓库包含多个子模块
...

---
- ### 提示 MERGE_HEAD EXIST

```
rm .git/MERGE_HEAD # 移除该文件
```
---
- ### git flow

> 示例

- 创建develop分支 (从master分支)

```
git branch develop
git push -u origin develop 
```

- 开始新Feature开发(从develop分支)

```
git checkout -b some-feature develop
git push -u origin some-feature    

# 做一些改动    
git status
git add some-file
git commit    
```

- 完成Feature

```
git pull origin develop
git checkout develop
git merge --no-ff some-feature
git push origin develop

git branch -d some-feature

# If you pushed branch to origin:
git push origin --delete some-feature
```

- 开始Relase

```
git checkout -b release-0.1.0 develop

# Optional: Bump version number, commit
# Prepare release, commit
```

- 完成Release(将release合并到master和develop分支)

```
git checkout master
git merge --no-ff release-0.1.0
git push

git checkout develop
git merge --no-ff release-0.1.0
git push

git branch -d release-0.1.0

# If you pushed branch to origin:
git push origin --delete release-0.1.0

git tag -a v0.1.0 master
git push --tags
```

- 开始Hotfix

```
git checkout -b hotfix-0.1.1 master
```

- 完成Hotfix

```
git checkout master
git merge --no-ff hotfix-0.1.1
git push

git checkout develop
git merge --no-ff hotfix-0.1.1
git push

git branch -d hotfix-0.1.1

git tag -a v0.1.1 master
git push --tags
```