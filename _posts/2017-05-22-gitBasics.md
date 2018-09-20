---
layout: post
title:  "Git基本用法"
date:   2017-05-22 15:10
categories: git
permalink: /archivers/gitba
---

![git](https://git-scm.com/images/logo@2x.png)

### 安装与配置

**使用 `apt-get` 方式安装**
```js
apt-add-repository ppa:git-core/ppa
apt-get update
apt-get install git
```

**生成 `ssh` 密钥**

```js
ssh-keygen -t rsa -C "jianqing2015@outlook.com"
cat ~/.ssh/id_rsa.pub
```

**配置用户信息与常用简写**

```js
git config --global user.name "jqing";
git config --global user.email "jianqing2015@outlook.com";
git config --global alias.amend "commit --amend -C HEAD";
git config --global alias.back "reset --soft HEAD^";
git config --global alias.br branch;
git config --global alias.ci commit -am;
git config --global alias.co checkout;
git config --global alias.edit "commit -am 'edit'";
git config --global alias.ls "log --pretty=format:'%ad %C(yellow)%h %C(reset)%s %C(red)%d' --decorate --date=format:'%Y/%m/%d %H:%M'";
git config --global alias.st status;
git config --global alias.zip "archive master --format=zip --output ../code.zip"
git config --global alias.showcommit "show --shortstat --pretty=format:'%ai %C(yellow)%h %C(reset)%s %C(red)%d'"
```

**其他配置**

```js
git config --global core.editor vim;
```

**编码问题**

```js
git config --global core.quotepath false
git config --global gui.encoding utf-8
```

### 操作

**提交**

1. 提交时显示所有 `diff` 信息  

    ```js
    git commit -v
    ```
1. 使用新的提交信息重做上一次 `commit`  

    ```js
    git commit --amend -m (message)
    ```

**分支**

1. 列出所有远程分支  

    ```js
    git branch -r
    ```

1. 列出所有本地分支和远程分支  

    ```js
    git branch -a
    ```

1. 新建一个分支，但依然停留在当前分支  

    ```js
    git branch (branch)
    ```

1. 新建一个分支，并切换到该分支  

    ```js
    git checkout -b (branch)
    ```

1. 新建一个分支，指向某个tag  

    ```js
    git checkout -b (branch) (tag)
    ```

1. 新建一个分支，指向指定commit  

    ```js
    git branch (branch) (commit)
    ```

1. 新建一个分支，与指定的远程分支建立追踪关系  

    ```js
    git branch --track (branch) (remote-branch)
    ```

1. 切换到指定分支，并更新工作区  

    ```js
    git checkout (branch-name)
    ```

1. 使分支的历史归零  

    ```js
    git checkout --orphan (branch)
    ```

1. 切换到上一个分支  

    ```js
    git checkout -
    ```

1. 建立追踪关系，在现有分支与指定的远程分支之间  

    ```js
    git branch --set-upstream (branch) (remote-branch)
    ```

1. 合并指定分支到当前分支  

    ```js
    git merge (branch)
    ```

1. 选择一个commit，合并进当前分支  

    ```js
    git cherry-pick (commit)
    ```

1. 删除分支  

    ```js
    git branch -d (branch)
    ```

1. 删除远程分支  

    ```js
    git push origin --delete (branch)
    git branch -dr (remote/branch)
    ```

**查看信息**

1. 显示commit历史，以及每次commit发生变更的文件  

    ```js
    git log --stat
    ```

1. 搜索提交历史，根据关键词  

    ```js
    git log -S (keyword)
    ```

1. 显示某个commit之后的所有变动，每个commit占据一行  

    ```js
    git log (tag) HEAD --pretty=format:%s
    git log --pretty=oneline
    ```

1. 显示某个commit之后的所有变动，其"提交说明"必须符合搜索条件  

    ```js
    git log (tag) HEAD --grep feature
    ```

1. 显示某个文件的版本历史，包括文件改名  

    ```js
    git log --follow (file)
    git whatchanged (file)
    ```

1. 显示指定文件相关的每一次diff  

    ```js
    git log -p (file)
    ```

1. 显示指定文件是什么人在什么时间修改过  

    ```js
    git blame (file)
    ```

1. 显示暂存区和上一个commit的差异  

    ```js
    git diff --cached (file)
    ```

1. 显示工作区与当前分支最新commit之间的差异  

    ```js
    git diff HEAD
    ```

1. 显示两次提交之间的差异  

    ```js
    git diff (first-branch)...(second-branch)
    ```

1. 显示某次提交的元数据和内容变化  

    ```js
    git show (commit)
    ```

1. 显示某次提交发生变化的文件  

    ```js
    git show --name-only (commit)
    ```

1. 显示某次提交时，某个文件的内容  

    ```js
    git show (commit):(filename)
    ```

**远程同步**

1. 下载远程仓库的所有变动  

    ```js
    git fetch (remote)
    ```

1. 显示所有远程仓库  

    ```js
    git remote -v
    ```

1. 推送所有分支到远程仓库  

    ```js
    git push (remote) --all
    ```

**撤销**

1. 恢复暂存区的指定文件到工作区  

    ```js
    git checkout -- (file)
    ```

1. 恢复某个commit的指定文件到暂存区和工作区  

    ```js
    git checkout (commit) (file)
    ```

1. 恢复暂存区的所有文件到工作区  

    ```js
    git checkout .
    ```

1. 重置暂存区的指定文件，与上一次commit保持一致，但工作区不变  

    ```js
    git reset (file)
    ```

1. 重置暂存区与工作区，与上一次commit保持一致  

    ```js
    git reset --hard
    ```

1. 重置当前分支的指针为指定commit，同时重置暂存区，但工作区不变  

    ```js
    git reset (commit)
    ```

1. 重置当前分支的HEAD为指定commit，同时重置暂存区和工作区，与指定commit一致  

    ```js
    git reset --hard (commit)
    ```

1. 重置当前HEAD为指定commit，但保持暂存区和工作区不变  

    ```js
    git reset --keep (commit)
    ```

1. 暂时将未提交的变化移除，稍后再移入  

    ```js
    git stash
    git stash pop
    ```

### *git* 技巧

1. 删除远程tag  

    ```js
    git push origin :refs/tags/(tag)
    ```

1. 添加tag时附带信息  

    ```js
    git tag -a v1.2 -m "version 1.4"
    ```

1. 忽略已追踪文件的变动及恢复  

    ```js
    git update-index --assume-unchanged (file)
    git update-index --no-assume-unchanged (file)
    ```

1. 输出最后一次提交的改变  

    ```js
    git archive -o ../updated.zip HEAD $(git diff --name-only HEAD^)
    ```

1. 开始一个无历史的新分支  

    ```js
    git checkout --orphan NEW_BRANCH_NAME_HERE
    ```

1. 修改历史提交  

    ```js
    git filter-branch -f --env-filter "GIT_AUTHOR_NAME='chuanfeng'; GIT_COMMITTER_NAME='chuanfeng';" HEAD
    ```

1. 清理工作目录  

    ```js
    du -hs .git/objects

    git gc --aggressive
    git fsck
    ```

### 高级操作

- [查看](/archivers/gitad)
