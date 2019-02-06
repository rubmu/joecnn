---
title: Git基本操作
date: 2017/12/26
tags: [git]
---

### 1. 取得 Git 仓库 
有两种取得 Git 项目仓库的方法，第一种是在现存的目录下，通过**初始化文件夹**来创建新的 Git 仓库： 
```bash
git init 
```
 
第二种是**克隆仓库**项目，Git 支持许多数据传输协议(git://, http(s)://, ssh)： 
```bash
$ git clone [url] new_project_name
```
 
### 2. 检查当前文件状态 
要确定**文件状态**，可以使用 git status 命令。文件状态分为： 
- untracked files: 未跟踪 
- changes to be committed: 已暂存 
- changed but not updated: 已修改，未暂存 
 
### 3. 追踪文件 
使用命令 git add 开始跟踪文件或文件夹： 
```bash
$ git add README 
```

> git add 命令是个多功能命令，根据目标文件的状态不同，此命令的效果也不同：可以用它开始跟踪新文件，或者把已跟踪的文件放到暂存区，还能用于合并时把有冲突的文件标记为已解决状态等。
 
### 4. 忽略文件 
当某些文件无需纳入 Git 管理时，可以创建一个名为 **.gitignore** 的文件，列出要忽略的文件模式。文件 .gitignore 的格式如下：
```bash 
    # 此为注释 – 将被 Git 忽略 
    *.a       # 忽略所有 .a 结尾的文件 
    !lib.a    # 但 lib.a 除外 
    /TODO     # 仅仅忽略项目根目录下的 TODO 文件，不包括 subdir/TODO 
    build/    # 忽略 build/ 目录下的所有文件 
     doc/*.txt # 会忽略 doc/notes.txt 但不包括 doc/server/arch.txt 
```
 对现有项目添加 git gitignore 不生效的解决办法： .gitignore 不生效解决方法

### 5. 查看已暂存和未暂存的变更内容 
如果想要查看具体修改内容，可以使用 git diff 命令： 
```bash
$ git diff 
```
 
此命令比较的是当前目录文件和暂存区快照之间的差异，也就是被修改后没有暂存的内容。 
如果想要查看暂存起来的文件和上次提交时的文件的差异，可以使用： 
```bash
$ git diff --cached 
$ git diff --staged  #v1.6.1+
```
 
### 6. 提交更新 
当将所有文件加入暂存区后可以提交了，运行命令： 
```bash
$ git commit -m "提交已经暂存(add)过的变更" 
```
 
给 git commit 加上 -a 选项，跳过暂存步骤，自动将所有已跟踪过的文件提交： 
```bash
$ git commit -a -m '直接提交变更，跳过暂存步骤' 
```
 
### 7. 撤消操作 
某些时候，提交完后才发现漏了几个文件，或者提交信息写错了，**修改最后一次提交**，可以使用 --amend 选项重新提交： 
```bash
$ git  commit  --amend  -m "修改备注信息" 
```
 
某些时间，不小心将不需要提交的文件使用了 git add，要**取消暂存文件**，可以使用以下命令： 
```bash
$ git  reset  HEAD  file_name 
```
 
如果对文件修改没必要，需要**撤销对文件的修改**，可以使用：
```bash
$ git  checkout  --  file_name 
```

注意此条命令会取消文件所有修改，回到上次提交的状态，需要谨慎使用。 
 
### 8. 移除文件 
移除文件，并从工作目录中删除文件，也支持正则匹配： 
```bash
$ git  rm  -f   file_name 
```
 
另一种情况，从 Git 中移除跟踪，但仍然保留文件，使用 --cached 选项即可： 
```bash
$ git  rm --cached file_name
```
 
### 9. 移动文件 
重命名或移动文件，都可以使用以下命令： 
```bash
$ git mv file_from  file_to 
```
 
其实运行 git mv 就相当于运行下面三条命令： 
```bash
$ mv  README.txt README 
$ git rm README.txt 
$ git add  README  
```
 
### 10. 查看提交历史 
想回顾下提交历史记录，可以使用 git log 命令查看。 
```bash
  -p : 显示每次提交的内容差异 
  -2：仅显示最近两次更新 
  --stat : 仅显示简要的增改行数统计 
  --pretty : 自定义显示风格 
      =oneline : 放在一行显示 
      =format:"%h - %an, %ar : %s" 格式输出 
  --since=2.weeks : 最近时间段 
  --author=xx :  作者 
  --committer=xx : 提交者 
```
使用图形化工具查看历史提交记录，使用命令 gitk 即可。 
 
### 11. 远程仓库的使用 
要查看当前配置有哪些远程仓库，可以使用 git remote 命令，会列出每个远程仓库： 
```bash
$ git remote -v 
```

要添加一个新的远程仓库，需要在 github 上创建一个空的仓库，指定一个简单的名字，以便引用，运行 git remote add [shortname] [url] 创建本地仓库: 
```bash
$ git remote add repository_name git://github.com/joecnn/imgcompact.git 
```
 
从远程仓库拉取数据到本地，但并**不合并**到当前工作分支： 
```bash
$ git fetch [remote-name] 
```
 
使用 git pull 命令拉取远程仓库并**合并**到本地分支： 
```bash
$ git pull [remote-name] 
```
 
推送数据到远程仓库： 
```bash
$ git push origin master 
```
 
查看远程仓库信息： 
```bash
$ git remote show origin
```
 
远程仓库的删除和重命名： 
```bash
$ git remote rename old_name new_name 
$ git remote rm rp_name
```