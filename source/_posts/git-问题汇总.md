---
title: git 问题汇总
date: 2017-10-22 21:33:21
tags:
- git
categories:
- 笔记
- git
---

- #### github/gitlab 多账户下设置ssh key
1. 使用命令：ssh-keygen -t rsa -C "注册git的邮箱"，命名时设置不一致的名字即可（id_rsa_*）
2. 将对应的id_rsa_*.pub文件内容放到相应的git账号的SSH Keys中
3. 配置config文件：在 .ssh/目录下新建config文件：==touch config== ，内容例子

<!--more-->

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
> #### Host 和 HostName 教程说Host可以随便起，但是亲测不行，需要保持一致，原因不明

- #### fatal: Unable to create 'XXX/.git/index.lock’: File exists

解决方法：在.git同级目录，执行rm -f .git/index.lock （或者rm -f git/index.lock） 删除后可提交

- #### git pull 失败 ,提示：fatal: refusing to merge unrelated histories

解决：
```
git pull origin master --allow-unrelated-histories
```

- #### HEAD detached at xxxx

> 场景：使用git checkout [tagName] 切换标签后出现此情况

> 问题说明: 此时HEAD处于游离状态(detached HEAD), 此时进行commit操作不会提交到任何分支上

> 解决: git branch 查看分支, git checkout xxx 回到需要的分支

- #### 提示 MERGE_HEAD EXIST

```
rm .git/MERGE_HEAD # 移除该文件
```

- #### git flow

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