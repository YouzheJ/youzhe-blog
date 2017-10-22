---
title: git 使用教程
date: 2017-10-22 21:17:54
tags:
- git
categories:
- 笔记
- git
---

- #### 概述

Git：
- Git是目前世界上最先进的分布式版本控制系统（没有之一）。
- 集中式/分布式：
    - 集中式版本控制系统，版本库是集中存放在中央服务器的，必须联网才能工作。
    - SVN 是目前用得最多的集中式版本库控制系统。
    - 分布式版本控制系统根本没有“中央服务器”，每个人的电脑上都是一个完整的版本库，这样，你工作的时候，就不需要联网了，因为版本库就在你自己的电脑上。

<!--more-->
- #### 配置


    - 在Windows上安装Git
    - 下载安装后，需要设置，在git bash 中输入：
    
    ```
    $ git config --global user.name "Your Name"
    $ git config --global user.email "email@example.com"
    ```

    > 注意git config命令的--global参数，用了这个参数，表示你这台机器上所有的Git仓库都会使用这个配置，当然也可以对某个仓库指定不同的用户名和Email地址。

- #### 版本库

    - 版本库又名仓库，英文名repository，你可以简单理解成一个目录，这个目录里面的所有文件都可以被Git管理起来，每个文件的修改、删除，Git都能跟踪，以便任何时刻都可以追踪历史，或者在将来某个时刻可以“还原”。
    - 首先，选择一个合适的地方，创建一个空目录：
    
    ```
    $ mkdir learngit
    $ cd learngit
    $ pwd
    ```
    > pwd命令用于显示当前目录。在我的Mac上，这个仓库位于/Users/michael/learngit。
    
    > 如果你使用Windows系统，为了避免遇到各种莫名其妙的问题，请确保目录名（包括父目录）不包含中文。
    
    - 第二步，通过git init命令把这个目录变成Git可以管理的仓库：
    
    ```
    $ git init
    ```
    > 当前目录下多了一个.git的目录，这个目录是Git来跟踪管理版本库的，没事千万不要手动修改这个目录里面的文件，不然改乱了，就把Git仓库给破坏了。

    > 如果你没有看到.git目录，那是因为这个目录默认是隐藏的，用ls -ah命令就可以看见。

    - 把文件添加到版本库
    新建readme.txt 文件，一定要放到learngit目录下（子目录也行）。
        - 第一步，用命令git add告诉Git，把文件添加到仓库：
        
        ```
        $ git add readme.txt
        ```
        
        > 没有提示即正确
        
        - 第二步，用命令git commit告诉Git，把文件提交到仓库：
        
        ```
        $ git commit -m "wrote a readme file"
        ```
        
        > git commit命令，-m后面输入的是本次提交的说明，可以输入任意内容，当然最好是有意义的，这样你就能从历史记录里方便地找到改动记录。
        
        commit可以一次提交很多文件，所以你可以多次add不同的文件，比如：
        
        ```
        $ git add file1.txt
        $ git add file2.txt file3.txt
        $ git commit -m "add 3 files."
        ```
        
        > ##### 注意：所有的版本控制系统，其实只能跟踪文本文件的改动
        
        > - 比如TXT文件，网页，所有的程序代码等等，Git也不例外。
        > - 版本控制系统可以告诉你每次的改动，比如在第5行加了一个单词“Linux”，在第8行删了一个单词“Windows”。
        > - 而图片、视频这些二进制文件，虽然也能由版本控制系统管理，但没法跟踪文件的变化，只能把二进制文件每次改动串起来，
        > - 也就是只知道图片从100KB改成了120KB，但到底改了啥，版本控制系统不知道，也没法知道。
        
        > 不幸的是，Microsoft的Word格式是二进制格式，因此，版本控制系统是没法跟踪Word文件的改动的，前面举的例子只是为了演示，如果要真正使用版本控制系统，就要以纯文本方式编写文件。
        
        > 因为文本是有编码的，比如中文有常用的GBK编码，日文有Shift_JIS编码，如果没有历史遗留问题，强烈建议使用标准的UTF-8编码，所有语言使用同一种编码，既没有冲突，又被所有平台所支持。
        
        > 使用Windows要特别注意：
        
        > - 千万不要使用Windows自带的记事本编辑任何文本文件。
        > - 原因是Microsoft开发记事本的团队使用了一个非常弱智的行为来保存UTF-8编码的文件，他们自作聪明地在每个文件开头添加了0xefbbbf（十六进制）的字符，
        > - 你会遇到很多不可思议的问题，比如，网页第一行可能会显示一个“?”，明明正确的程序一编译就报语法错误，等等，都是由记事本的弱智行为带来的。
        > - 建议你下载Notepad++代替记事本，不但功能强大，而且免费！记得把Notepad++的默认编码设置为UTF-8 without BOM即可。

- #### 版本管理

    - 修改文件内容
    
    > 运行git status命令看看结果：
    
    ```
    $ git status
    ```
    
    - 查看仓库当前的状态
    
    > 用git diff命令查看具体修改
    
    ```
    $ git diff readme.txt
    ```
    > git diff顾名思义就是查看difference，显示的格式正是Unix通用的diff格式
    
    - 提交修改和提交新文件是一样的两步
        - 第一步是git add：
        
        ```
        $ git add readme.txt
        ```
        
        > 同样没有任何输出。
        
        - 在执行第二步git commit之前，我们再运行git status看看当前仓库的状态：
        
        ```
        $ git status
        # On branch master
        # Changes to be committed:
        #   (use "git reset HEAD <file>..." to unstage)
        #
        #       modified:   readme.txt
        #
        ```
        
        > git status显示，将要被提交的修改包括readme.txt，
        
        - 下一步，就可以提交了：
        
        ```
        $ git commit -m "add distributed"
        ```
    
    - 版本回退
    
    > 用git log命令查看历史记录：
    
    ```
    $ git log
    ```
    
    > 加上--pretty=oneline参数，可减少输出的信息
    
    > 版本回退,使用git reset命令：
    
    ```
    $ git reset --hard HEAD^
    
    # HEAD is now at ea34578 add distributed       (ea34578 位版本id)
    ```
    
    > 在Git中，用HEAD表示当前版本，也就是最新的提交3628164...882e1e0，上一个版本就是HEAD^，上上一个版本就是HEAD^^，当然往上100个版本写成HEAD~100。
    
    - 查看文件内容：
    
    ```
    $ cat readme.txt
    ```
    
    > 注意：只要上面的命令行窗口还没有被关掉，就可以回退到新的版本。
    
    ```
    $ git reset --hard 3628164
    ```
    > (版本号没必要写全，前几位就行，Git会自动去找。)
    
    - 查看命令历史，以便确定要回到未来的哪个版本：
    
    ```
    $ git reflog
    ```
    
    > 当你用$ git reset --hard HEAD^回退到add distributed版本时，再想恢复到append GPL，就必须使用以上命令找到append GPL的commit id。

- #### 工作区（Working Directory）

> 在电脑里能看到的目录，比如learngit文件夹就是一个工作区。

- #### 版本库（Repository）

> 工作区有一个隐藏目录.git，这个不算工作区，而是Git的版本库。

- #### 暂存区（stage或index）

> 暂存区存放在Git的版本库里，还有Git为我们自动创建的第一个分支master，以及指向master的一个指针叫HEAD。

> 往git版本库添加文件时：

- 第一步用git add把文件添加进去，实际上就是把文件修改添加到暂存区；
- 第二步用git commit提交更改，实际上是把暂存区的所有内容提交到当前分支。

- 修改管理

> git管理的是修改而非文件，所以commit前需要将修改add到暂存区。

- 撤销修改

    - 撤销工作区修改
    
    ```
    $ git checkout -- readme.txt
    ```
    
    > 命令的意思是把readme.txt文件在工作区的修改全部撤销，这里有两种情况：
    > - 一种是readme.txt自修改后还没有被放到暂存区，现在，撤销修改就回到和版本库一模一样的状态；
    > - 一种是readme.txt已经添加到暂存区后，又作了修改，现在，撤销修改就回到添加到暂存区后的状态。

    - 撤销暂存区的修改，重新放回到工作区。
    
    ```
    $ git reset HEAD readme.txt
    ```

- 删除文件

    - 工作区删除文件
    
    ```
    $ rm test.txt
    ```
    
    > 然后可以有一下两种操作：
    > 1. 在版本刻库删除
    
    ```
    $ git rm test.txt
    ```
    
    > 2. 从版本库恢复到工作区
    
    ```
    $ git checkout -- test.txt
    ```
    
- 远程仓库

> 本地Git仓库和GitHub仓库之间的传输是通过SSH加密的，所以，需要一点设置：

第1步：创建SSH Key。

在用户主目录下，看看有没有.ssh目录:

如果有，再看看这个目录下有没有id_rsa和id_rsa.pub这两个文件，如果已经有了，可直接跳到下一步。

如果没有，打开Shell（Windows下打开Git Bash），创建SSH Key：

```
$ ssh-keygen -t rsa -C "youremail@example.com"
```

你需要把邮件地址换成你自己的邮件地址，然后一路回车，使用默认值即可，由于这个Key也不是用于军事目的，所以也无需设置密码。

如果一切顺利的话，可以在用户主目录里找到.ssh目录，里面有id_rsa和id_rsa.pub两个文件，这两个就是SSH Key的秘钥对
> id_rsa是私钥，不能泄露出去，id_rsa.pub是公钥，可以放心地告诉任何人。

第2步：登陆GitHub，打开“Account settings”，“SSH Keys”页面：

然后，点“Add SSH Key”，填上任意Title，在Key文本框里粘贴id_rsa.pub文件的内容。

点“Add Key”，你就应该看到已经添加的Key。

添加远程仓库

首先，登陆GitHub，然后，在右上角找到“Create a new repo”按钮，创建一个新的仓库。

在Repository name填入learngit，其他保持默认设置，点击“Create

repository”按钮，就成功地创建了一个新的Git仓库。

根据GitHub的提示，在本地的learngit仓库下运行命令：

```
$ git remote add origin git@github.com:michaelliao/learngit.git
```

> 注意，把上面的michaelliao替换成自己的GitHub账户名。

添加后，远程库的名字就是origin，这是Git默认的叫法，也可以改成别的。

下一步，就可以把本地库的所有内容推送到远程库上：	

```
$ git push -u origin master
```

把本地库的内容推送到远程，用git push命令，实际上是把当前分支master推送到远程。

由于远程库是空的，我们第一次推送master分支时，加上了-u参数，Git不但会把本地的master分支内容推送的远程新的master分支，还会把本地的master分支和远程的master分支关联起来，在以后的推送或者拉取时就可以简化命令。

从现在起，只要本地作了提交，就可以通过命令：

```
$ git push origin master
```

从远程库克隆

假设我们从零开发，那么最好的方式是先创建远程库，然后，从远程库克隆。

首先，登陆GitHub，创建一个新的仓库，名字叫gitskills.

勾选Initialize this repository with a
README，这样GitHub会自动为我们创建一个README.md文件。

现在，远程库已经准备好了，下一步是用命令git clone克隆一个本地库。

```
$ git clone git@github.com:michaelliao/gitskills.git
```

#### 分支管理

- 首先，我们创建dev分支，然后切换到dev分支：

```bash
$ git checkout -b dev
```

> git checkout命令加上-b参数表示创建并切换，相当于以下两条命令：

```
$ git branch dev
$ git checkout dev
```

- 用git branch命令查看当前分支：

```
$ git branch
```

> git branch命令会列出所有分支，当前分支前面会标一个*号。

- 提交：

```
$ git add readme.txt 
$ git commit -m "branch test"
```

- 把dev分支的工作成果合并到master分支上：

```
$ git merge dev
```

- 合并完成后，就可以放心地删除dev分支了：

```
$ git branch -d dev
```

- 解决冲突

> 当Git无法自动合并分支时，就必须首先解决冲突。解决冲突后，再提交，合并完成。

> 用git log --graph命令可以看到分支合并图。

bug分支

- 临时储存

```
$ git stash
```

- 查看临时储存的列表

```
$ git stash list
```

- 恢复，有两个办法：

1. 一是用git stash apply恢复，但是恢复后，stash内容并不删除，需用git stash drop来删除；
2. 另一种方式是用git stash pop，恢复的同时把stash内容也删了：

>可以多次stash，恢复的时候，先用git stash list查看，然后恢复指定的stash，用命令：

```
$ git stash apply stash@{0}
```

feature分支

- 开发一个新feature，最好新建一个分支；
- 如果要丢弃一个没有被合并过的分支，可以通过git branch -D <name>强行删除。


#### 多人协作

- 查看远程库的信息，用git remote：

```
$ git remote
```

> 用git remote -v显示更详细的信息：

```
$ git remote -v
```

- 推送分支：

```
$ git push origin master
```

- 推送其他分支，比如dev，就改成：

```
$ git push origin dev
```

- 多人协作的工作模式通常是这样：
  1. 首先，可以试图用git push origin branch-name推送自己的修改；
  2. 如果推送失败，则因为远程分支比你的本地更新，需要先用git pull试图合并；
  3. 如果合并有冲突，则解决冲突，并在本地提交；
  4. 没有冲突或者解决掉冲突后，再用git push origin branch-name推送就能成功！
> 如果git pull提示“no tracking information”，则说明本地分支和远程分支的链接关系没有创建，用命令git branch --set-upstream branch-name origin/branch-name。