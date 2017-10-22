---
title: git 命令汇总
date: 2017-10-22 20:44:38
tags: 
- git
comments: true
categories: 
- 笔记
- git
---

#### 基本命令

- git init: 初始化一个git仓库
- touch .gitignore ：新建.gitignore 文件

- clone

```
git clone <远程地址> # 克隆远程仓库
git clone <远程地址> <目录名> # 克隆远程仓库到指定目录中
git clone <仓库地址> -b <分支名或标签> # 克隆指定分支或标签
```
<!--more-->
- 添加用户

```
git config --global user.name "Your Name"
git config --global user.email "email@example.com"
```
- add 

```
git add <file> ：添加文件到git仓库。
git add -u：将文件的修改、文件的删除，添加到暂存区。
git add .：将文件的修改，文件的新建，添加到暂存区。
git add -a：将文件的修改，文件的删除，文件的新建，添加到暂存区。
```
- commit

```
git commit -m "xxx" # 添加说明， 单行
git commit -a # 添加说明， 使用vim， 可以有多行
git commit --amend 修改已提交的commit, push过就不行了
```

- push

```
git push -u origin master # 第一次推送master分支的所有内容
git push origin master # 推送新版本至远程仓库
git push orign local_branch:remote_branch -u # 第一次推送时关联远程仓库
git push orign local_branch:remote_branch # 将本地的当前分支推送得到远程指定的分支，如果远程不存在则自动创建
git push origin v0.1.2 # 将v0.1.2标签提交到git服务器
git push origin --tags # 将本地所有标签一次性提交到git服务器
git push orign :<remote_branch or tag> # 删除远程仓库的分支/标签
```

- remote

```
git remote add origin git@server-name:path/repo-name.git：关联远程仓库
git remote remove upstream：删除上游
```

- branch

```
git branch：查看分支
git branch <name>：创建分支
git branch <name> <commit_id>：以某commit创建新的分支
git branch -d <name>：删除分支
```

- checkout

```
git checkout -- file：在工作区中，丢弃工作区的修改
git checkout <name>：切换分支
git checkout -b <name>：创建+切换分支
git checkout --merge <name>：将当前分支同步切换大 name 分支
git checkout -b local_branch origin/remote_branch 将本地不存在的远程分支拉至本

git reset HEAD file：将暂存区的修改回到工作区中，再用git checkout -- file 可丢弃暂存区的修改
```

- log

> ##### 英文状态下按q退出

```
git log # 查看提交历史，以便回到过去
git log –-graph # 当前分支提交ID的树状图
git log --oneline # 简单的显示
```

- git status ：查看git的状态
- git diff ： 查看修改的内容
- git reflog：查看历史命令，以便回到未来
- git show [tagname] 查看标签信息

- pull

```
git pull origin master # 将远程的master分支与本地的master合并
git pull origin remote_branch:local_branch # 将远程的remote_branch与本地的local_branch合并

git branch --set-upstream master origin/next # 将本地的master分支与origin/next分支建立追踪
```

 - reset
 
```
git reset --hard commit_id：版本回退，HEAD指向当前版本，HEAD^指向上一个版本
```

- merge

```
git merge <name>：合并某分支到当前分支
git merge --no-ff <name>  保存你之前的分支历史
git merge --squash <name> 使用squash方式合并，把多次分支commit历史压缩为一次
文件已经同合并后一样了，但不移动HEAD，不提交。需要进行一次额外的commit来“总结”一下，然后完成最终的合并
```

- git revert commit_id：版本回退

> ##### reset 和 revert的区别
> - revert是放弃指定提交的修改，但是会生成一次新的提交，需要填写提交注释，以前的历史记录都在
> - reset是指将HEAD指针指到指定提交，历史记录中不会出现放弃的提交记录

- tag 

```
git tag 列出所有标签
git tag v0.1.2-light 创建轻量标签
git tag -a v0.1.2 -m "0.1.2版本" 创建附注标签
git tag -d v0.1.2 删除标签
git tag -a v0.1.2 9fbc3d0 给指定的commit打标签（通过git log获取）
```

- rebase

```
git rebase -i "commit id"^ # 注意末尾有 ^， 示例： git rebase -i "5b3ba7a"^
git rebase -i 处理多个commit
```

- clean

```
git clean -n  # 显示 将要 删除的 文件 和  目录
git clean -df # 删除 文件 和 目录
git clean -f  # 删除 文件
```

- #### ^ 和 ~ 的区别

1. "^"代表父提交,当一个提交有多个父提交时，可以通过在"^"后面跟上一个数字，表示第几个父提交，"^"相当于"^1".
2. ~<n>相当于连续的<n>个"^".
3. checkout只会移动HEAD指针，reset会改变HEAD的引用值。

---
#### 使用例子
> ##### 部分命令可以按照实际需求更改参数

- #### 一般的操作流程

    - 初始化仓库
    
    ```
    git init # 创建一个本地仓库
    git remote add origin git@server-name:path/repo-name.git # 关联远程仓库
    
    # 或者克隆远程仓库
    git clone xxx
    ```
    - 添加修改并提交到本地
    
    ```
    git add .
    git commit -m "xxx"
    ```
    
    - 提交到远程
    
    ```
    # 第一次提交需要加上 -u
    git push origin master # 提交本地master分支到远程master分支
    git push origin local_branch:remote_branch # 提交本地local_branch分支到远程remote_branch分支(远程不存在会自动创建)
    ```

- #### 版本回退

```
git log # 查看历史版本id

# 使用reset
git reset --hard commit_id # 版本回退，HEAD指向当前版本
# 或者使用revert
git revert commit_id # 版本回退, 但是会生成新的提交
```

- 回滚远程仓库

1. 删除最后一次提交

```
# 保留原来的提交历史
git revert HEAD # 放弃提交的修改， 并生成新的提交
git push origin master

# 放弃原来的提交历史
git reset --hard HEAD^
git push origin master -f # reset后，需要强制提交
```

2. 删除历史的某次提交

```
git rebase -i "commit_id"^ # "commit id"替换为想要删除的提交的"commit id"，需要注意最后的^号，意思是commit id的前一次提交
# 然后在编辑框中删除你想要删除的提交所在行

git push origin master -f
```

3. 修改历史某次提交

> 在 2 的基础上将编辑框中的 pick 改为 edit， 然后保存退出

修改文件， 然后执行以下操作： 

> 如果将多行改为 edit， 需要执行以下操作多次

```
git add .
git commit --amend
git rebase --continue
```

- #### push前修改commit

```
git commit --amend
```

- #### 合并分支时不合并commit

```
git merge --squash <name>
```

- #### 修改远程地址

```
git remote remove upstream # 先删除上游
git remote add origin <远程地址> # 关联远程仓库
```

- #### 删除远程仓库的分支或标签

```
git push orign :<remote_branch or tag> # 删除远程仓库的分支/标签
```

- #### 撤销操作

    - 丢弃工作区的修改
    
    ```
    git checkout -- file # 丢弃指定文件
    git checkout -- . # 丢弃所有
    ```
    - 丢弃commit
    
    ```
    git reset HEAD file # 将暂存区的修改回到工作区中
    ```
    - 撤销指定文件到指定版本
    
    ```
    # 查看指定文件的历史版本
    git log <filename>
    # 回滚到指定commitID
    git checkout <commitID> -- <filename>
    ```