---
title: Git工作常用命令
date: 2021-02-21 12:46:32
tags: git 
---

### 2021年02月21日新增

* git checkout -- 文件名 修改文件时，希望放弃此次修改

  git checkout [commitid] -- 文件名 将单个文件撤回到某个commitid提交的文件

* git reset HEAD 文件名 git add .后，需要从暂存区撤回某个文件



* git reset --hard [commit_id] 撤回到某个版本内容，此时工作区的内容将替换为commit_id版本的内容



* git reset --soft HEAD~1/N/[commit_id] 撤回commitMsg信息，工作区的内容并不会发生变化 ，撤回上一次的提交信息后，此时的文件状态变为等待add



* git stash 文件处于暂存区时，通过该 命令可暂时存放封存



* git stash apply [保存的名字] 释放保存的



* git stash drop [保存的名字] 删除封存的记录



* git stash -u 如果 是没有追踪的文件（新加文件）也封存



* git stash --index 释放stash时，不仅将工作区代码恢复，暂存区代码也恢复，此时处于commit状态



* git merge --abort 合并分支出现错误时，可用此命令撤消合并



* git stash list 查看所有封存的列表



* git tag '内容' [commit_id] 为某个版本添加标签、版本等信息



* git rebase 变基，当提交 的commit 与pull拉取出现分歧 时，可通过变基分析



* git [相关命令 remote] --help 打开帮助，此时会打开一个虚拟端口显示



* git remote add origin url 关联远程git仓库地址



* git cherry-pick 某分支的CommitID 将某个分支提交的文件复制到当前分支中，合并成功后,log中也会有该提交信息



* git checkout --track origin/branchName 切换到远程分支，--track 关联远程分支内容



* git branch -r 查看远程所有分支



* git rm file_path 删除暂存区与工作区的文件



* git rm --cache 文件名 删除暂存区文件，但工作区的文件不操作，保留修改

* git rm -f 文件名   新版git，移除文件和暂存区文件命令

* git log 查看日志



* git log -p 与git diff类似 ，将会把提交 日志的具体修改展示出来



* git log --oneline 日志简短输出 [commitid][提交描述]
* git log --author=shao.yuhong  筛选出shao.yuhong提交的日志记录
* git log --graph 日志以图表的形式展示
* git log --author=shao.yuhong --name-only  混用查看shao.yuhong提交的日志记录，且只列出提交的文件列表
* git show [commitid]  列出修改的文件详情内容
* git show [commit] --name-only  列出修改的文件，不含内容
* git push --set-upstream origin 分支名   **创建一个新分支后，向远程推送这个分支**
* git push origin --delete  分支名    **删除远程中的一个分支**
* 文件状态：both Modiffed 冲突文件 Moddifile 编辑的文件 new file 新增文件，未被追踪的文件



### 2022年4月7日新增

Q：多分支开发，合并主分支master到当前自己的分支

* git fetch  拉代码，并不会合并，也不会冲突

* git rebase origin 主分支master(不会冲突则会获取最新的commit和代码)

  #### 冲突的情况

  * 解决冲突
  * git add .   标记冲突已解决
  * git rebase --continue    继续获取最新代码，因为是当出现冲突后只会合并部分文件，解决后需要继续获取最新代码

Q：日志提交规范

* feat:[空格]描述   增加
* del:[空格]..          删除 
* mod:[空格]         修改
* update:[空格]     更新
* fix:[空格 ]             修复问题
* style:[空格]          样式
* docs:[空格]          文档 