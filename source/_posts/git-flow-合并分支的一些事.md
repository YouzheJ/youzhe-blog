---
title: git flow 合并分支的一些事
date: 2017-12-17 20:25:12
tags:
- git
categories:
- 文章
- git
---

### 前言

最近工作中用到了 git flow 进行开发，确实按照规范进行开发可以避免一些不必要的麻烦，但是在实际使用中，还是遇到了一些问题。这里主要想谈谈分支合并时，避免和解决冲突的一些操作。

<!--more-->

### 一些准备工作

本文会以一个简单的实例进行说明，如果你已经熟悉git的操作，可以跳过准备部分

> 这里只是以本地进行演示

- 首先本地有两条分支

```bash
* develop
master
```

- 在develop分支里有一个test文件夹，然后在test文件夹里面有a.js文件

```bash
# a.js

HELLO WORLD!
```

- 从develop分支创建子分支feature/test1

```bash
$ git checkout -b feature/test1
```

> '$' 开头表示git命令，并且 '$' 并不需要输入，下同

- 修改test/a.js，然后提交

```bash
# a.js 将 WORLD 改为 GIT FLOW
HELLO GIT FLOW!
```

```bash
$ git add .
$ git commit -m "test1"
```

- 切回develop分支，再从develop分支创建子分支feature/test2

```bash
git checkout develop
git checkout -b feature/test2
```

- 修改test/a.js，然后提交

```bash
# a.js 将 WORLD 改为 GIT
HELLO GIT!
```

```bash
$ git add .
$ git commit -m "test2"
```

- 我们先将feature/test2 分支合并到fevelop分支上

```bash
$ git checkout develop # 先切回develop分支
$ git merge feature/test2
```

- 下面将以如何合并feature/test1分支进行展开


### 分支合并

#### 1. merge

> 当我们要开发一个新功能时，首先会从develop分支创建出一条新分支进行开发，在这个功能开发完成后，就需要将这条子分支合并回develop分支上，最常用的合并方式就是merge了

上面的准备中已经用到了merge合并test2分支，然后我们继续合并test1分支

```bash
$ git merge feature/test1
```

这时git就会提示

```bash
Auto-merging test/a.js
CONFLICT (content): Merge conflict in test/a.js
```

test/a.js 冲突了！！然后打开文件，就会看到

```bash
<<<<<<< HEAD
HELLO GIT!
=======
HELLO GIT FLOW!
>>>>>>> feature/test1
```

因为在两个子分支上修改了同一个地方，所以合并时就会产生冲突，解决冲突很简单，就是删掉其中一个就行了。
这里我选择保留test1的修改，所以需要删掉HEAD(即当前提交)的内容

```bash
<<<<<<< HEAD
HELLO GIT!
=======
```

> **注意，解决完冲突后，需要将 >>> / === / <<< ,这些开头的行给删掉（自己写的除外），这些是git生成的提示标识**

解决后 test/a.js 就变成

```bash
HELLO GIT FLOW!
```

这时再提交一下修改就完成合并了。

但是使用merge可能有以下问题：

1. 如果冲突过多，一个个解决也是挺麻烦的
2. merge是git自动完成的，有时候合并的结果并不是你想要的

其实产生这些问题的主要原因是，merge的时候是将所有文件合并，那有没有单独合并一个文件或文件夹的方法呢？

#### 2. checkout branch file/dir

在develop分支上，我们先将代码回退到合并前（按实际情况使用下面命令之一）

```bash
$ git reset --hard HEAD # 解决冲突后没有提交，使用这个
$ git reset --hard HEAD^ # 解决冲突后提交了，使用这个
```

然后，重新合并test1分支

```bash
$ git checkout feature/test1 test/a.js
```

> 命令说明：checkout 后面为分支名，然后是文件或者文件夹的路径，需要注意的是，如果文件或文件夹的名字与分支名相同，需要加上 '--', 如：合并test分支下test目录的所有文件 git checkout test -- test

使用上述命令就会将feature/test1分支下的test/a.js文件覆盖develop分支的test/a.js文件，然后我们commit一下就完成合并了。

因为是直接覆盖，所以就没有冲突一说了。

但是又有问题了，直接覆盖的话，可能会将之后的一些内容给覆盖了。

那有没有方法可以解决这个问题呢？

#### 3. checkout branch file/dir 与分支结合使用

我们再次回退提交(同上)，然后从develop分支创建一条临时分支

```bash
$ git checkout -b develop_tmp
```

然后，按照上述操作将test1分支的test/a.js文件覆盖develop_tmp分支，并进行提交

```bash
$ git checkout feature/test1 test/a.js
$ git commit -m "checkout test1"
```

接着，切回develop分支，并合并develop_tmp分支，合并完成后，删除临时分支

```bash
$ git checkout develop
$ git merge develop_tmp
$ git branch -d develop_tmp
```

通过这个临时分支，就做到了只将需要的修改合并到develop分支上。

到这里，我们已经将合并细致到文件级别了，已经足够好了。

但是，由于要再创建一条临时分支，而且用到了merge，不能完成掌控这个合并，那有没有更加细致的方式呢？

#### 4. checkout --patch branch file/dir

再次回退提交，在develop分支上输入以下命令

```bash
$ git checkout --patch feature/test1 test/a.js # 增加了patch参数
```

git就会显示以下内容

```bash
diff --git b/test/a.js a/test/a.js
index 9e9827d..f8d3752 100644
--- b/test/a.js
+++ a/test/a.js
@@ -1 +1 @@
-HELLO GIT!
+HELLO GIT FLOW!
Apply this hunk to index and worktree [y,n,q,a,d,/,e,?]? 
```

上面显示的中间内容

```bash
-HELLO GIT!
+HELLO GIT FLOW!
```

就是这个区块的具体修改，前面的减号表示删除，加号表示增加。

git 给出了8个可选操作（git版本不同可能不一样）

```bash
[y,n,q,a,d,/,e,?]
```

输入 ? 或者直接enter就会打印帮助信息

```bash
# 中文翻译仅供参考
y - apply this hunk to index and worktree # 应用当前区块
n - do not apply this hunk to index and worktree # 放弃当前区块
q - quit; do not apply this hunk or any of the remaining ones # 不使用任何区块，直接退出
a - apply this hunk and all later hunks in the file # 在当前文件下应用此区块以及后续的所有区块
d - do not apply this hunk or any of the later hunks in the file # 在当前文件下不应用此区块以及后续的所有区块
g - select a hunk to go to # 选择一个区块
/ - search for a hunk matching the given regex # 使用正则搜索区块
j - leave this hunk undecided, see next undecided hunk # 先跳过当前区块，并跳转到下一个未处理区块
J - leave this hunk undecided, see next hunk # 先跳过当前区块，并跳转到下一个区块
k - leave this hunk undecided, see previous undecided hunk # 先跳过当前区块，并跳转到上一个未处理区块
K - leave this hunk undecided, see previous hunk # 先跳过当前区块，并跳转到上一个区块
s - split the current hunk into smaller hunks # 切割当前区块
e - manually edit the current hunk # 编辑当前区块
? - print help # 显示帮助信息
```

这里因为只有一个区块，所以直接输入 y , 然后commit一下就完成合并了。

可以看到，加入了patch参数，可以非常细致地进行合并操作，可以完全掌控整个合并过程，甚至可以做到只合并某一块的修改。

这个处理方式可以说没有任何副作用，但是，如果修改过多的话，同样也是一件挺麻烦的事情。。

### 总结

可以看到，git的操作非常强大，并且非常灵活。

上面也只是我找到的一些解决方案而已，具体需要使用什么方式进行合并，还是得看具体的使用场景。

当然，如果不使用命令行，而是使用界面工具，事情可能就没有这么麻烦了。

### 参考资料

- [Git教程](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)
- [Git Flow工作流程](http://www.jianshu.com/p/9a76e9aa9534)
- [git中如何合并某个指定文件](http://blog.csdn.net/caolaosanahnu/article/details/32141103)
- [git小技巧--如何从其他分支merge个别文件或文件夹](https://segmentfault.com/a/1190000008360855)