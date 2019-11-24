

### 工作区和暂存区

工作区 => [git add files]  => 暂存区 => [git commit] => 历史版本

>  git add <文件 或 文件夹>	  -- 将文件添加到暂存区
>
> git add -u  -- 将工作区更新的文件全部都添加到 暂存区

> git status  	-- 查看状态

>  git log	-- 查看提交日志

> git commit -m"msg"  -- 提交

### 给文件重命名的简洁方法

> git reset --hard   -- 清除工作区和暂存区修改

> git mv readme  readme.md  -- 修改文件名

### 通过 git log 查看版本演变历史

> git log --oneline   -- 每条 commit 只显示一行 （当前分支）
>
> git log -n4 --online  -- 查看最近4次commit（当前分支）
>
> git branch -v  -- 查看本地所有分支
>
> git checkout -b branchName   242341ds3sfa   -- 创建分支 基于 242341ds3sfa 提交
>
> git log --all  -- 查看所有分支的历史提交
>
> git log --all --graph  --查看所有分支演进历史  （图形）
>
> git log --oneline --graph -n4
>
> git help --web log   以 web 的方式查看 log 命令的 帮助文档

### 图形界面工具

> wish

###探密.git 目录

```shell
huwei:.git huwei$ pwd
/Users/huwei/Documents/git-study/.git
huwei:.git huwei$ ll
total 24
drwxr-xr-x  10 huwei  staff  320 11 24 21:02 .
drwxr-xr-x   3 huwei  staff   96 11 24 21:02 ..
-rw-r--r--   1 huwei  staff   23 11 24 21:02 HEAD
drwxr-xr-x   2 huwei  staff   64 11 24 21:02 branches
-rw-r--r--   1 huwei  staff  137 11 24 21:02 config
-rw-r--r--   1 huwei  staff   73 11 24 21:02 description
drwxr-xr-x  12 huwei  staff  384 11 24 21:02 hooks
drwxr-xr-x   3 huwei  staff   96 11 24 21:02 info
drwxr-xr-x   4 huwei  staff  128 11 24 21:02 objects
drwxr-xr-x   4 huwei  staff  128 11 24 21:02 refs
huwei:.git huwei$ cat HEAD 
ref: refs/heads/master
huwei:.git huwei$ cat config 
[core]
	repositoryformatversion = 0
	filemode = true
	bare = false
	logallrefupdates = true
	ignorecase = true
	precomposeunicode = true
huwei:git-study huwei$ cat .git/refs/heads/master 
8e2874ebfc2ddd6f5a3153e46ba16f8852b7252e
huwei:git-study huwei$ git branch -av
* master 8e2874e add line
```

> HEAD 文件 保存的是 当前分支信息
>
>config  文件保存 配置信息
>
>git config --local --list 查看所有配置信息
>
>git config --local user.name  查看用户名
>
>git config --local user.name "huwei" 更新用户名为 huwei
>
>git branch -av  查看当前分支
>
>.git/refs/heads/master  文件 master分支指向的 commit 



### commit 、tree、blob 三个对象之间的关系

```shell
huwei:git-study huwei$ git branch -av
* master 9c661dd add github
  temp   f77011a add line github
huwei:git-study huwei$ git cat-file -t 9c661dd
commit
huwei:git-study huwei$ git cat-file -p 9c661dd
tree e1c10a01138606397c840ab7eca8e776df6bfe84
parent 8e2874ebfc2ddd6f5a3153e46ba16f8852b7252e
author huwei <huwei@huwei.lan> 1574605397 +0800
committer huwei <huwei@huwei.lan> 1574605397 +0800

add github
huwei:git-study huwei$ git cat-file -t e1c10a0113860
tree
huwei:git-study huwei$ git cat-file -p e1c10a0113860
100644 blob e691e7bad95a813382d6ec4dae8013badf3757eb	readme
huwei:git-study huwei$ git cat-file -t e691e7bad95a8
blob
huwei:git-study huwei$ git cat-file -p e691e7bad95a8
hello git
hello github
hello gitlab
huwei:git-study huwei$ 
```

>  commit   下面包括一个 tree
>
> tree 下面包括 tree  或 blob
>
> blob 是具体的文件
>
> git cat-file -t  id   查看id 对于的类型  又  commit  、tree 、 blob
>
> git cat-file -p id  查看对于id 的内容

### 分离头指针情况下的注意事项

```shell
huwei:git-study huwei$ git log --oneline
9c661dd (HEAD -> master) add github
8e2874e add line
f9d7448 add readme file
huwei:git-study huwei$ git checkout 8e2874e
Note: checking out '8e2874e'.

You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by performing another checkout.

If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -b with the checkout command again. Example:

  git checkout -b <new-branch-name>

HEAD is now at 8e2874e... add line
```

> git checkout 历史提交ID    -- 会进入 分离头指针状态  'detached HEAD' state  ，正工作在没有分支的状态下
>
> 修改文件后，提交后

```shell
huwei:git-study huwei$ git log --oneline
91e191e (HEAD) remove github
8e2874e add line
f9d7448 add readme file
huwei:git-study huwei$ git checkout master
Warning: you are leaving 1 commit behind, not connected to
any of your branches:

  91e191e remove github

If you want to keep it by creating a new branch, this may be a good time
to do so with:

 git branch <new-branch-name> 91e191e

Switched to branch 'master'
huwei:git-study huwei$ 
```

> 当在 分离头指针状态下 ，提交的commit 不属于任何分支，如果切换到 其他分支， 会警告提示
>
> 如果需要 将分离头指针的 commit  连接到某一个分支，使用 git branch <new-branch-name> 91e191e  
>
> 注意： 必须是一个新的分支，如果不做连接， 会存在丢失 的风险



###进一步理解 HEAD 和 branch

```shell
huwei:git-study huwei$ git checkout -b fix2 fix1
Switched to a new branch 'fix2'
huwei:git-study huwei$ git log -n1
commit 91e191e478b251c0b4027d994a6322a215269886 (HEAD -> fix2, fix1)
Author: huwei <huwei@huwei.lan>
Date:   Sun Nov 24 22:49:36 2019 +0800

    remove github
huwei:git-study huwei$
```



> git diff id1 id2    --  查看 两个commit id 的 差异
>
> git diff HEAD HEAD^1

### 删除不使用的分支

```shell
huwei:git-study huwei$ git branch -d fix2
error: The branch 'fix2' is not fully merged.
If you are sure you want to delete it, run 'git branch -D fix2'.
huwei:git-study huwei$ git branch -D fix2
Deleted branch fix2 (was 91e191e).
huwei:git-study huwei$ 
```

> git branch -d branchName 
>
> git branch -D branchName 

### 修改commit的message

> git commit --amend   -- 修改最近一次提交的message

### 合并连续的commit

> git rebase -i  上一个ID

### 比较暂存区和HEAD所含文件的差异

> git diff --cached   -- 查看暂存区和 HEAD之间的差异

### 比较工作区河暂存区所含文件的差异

> git diff    --比较工作区河暂存区的区别



