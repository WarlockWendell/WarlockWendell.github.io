---
title: git基础
date: 2020-1-15 22:20:22
tag: [git]
---

git是一种很好用且流行版本管理软件，这里介绍一下它的使用方法

<!--more-->

##  1.安装方法

- Windows：直接download，但还是Windows的gitGUI 界面十分丑陋，使用git bash体验更佳
-  Linux ： sudo apt-get install git

## 2. 使用的一些基本方法

- 进入本地你想要变成仓库的文件夹 输入git init使之变成一个仓库

-  初次使用时要先输入用户的信息

  ```bash
  git config user.name "your_name"
  git config user.email "your_email"
  ```

  设置之后，可使用

  ```bash
  git config user.name
  git config user.email
  ```

  查看设置是否正确

- 使用 git add filename 来将文件变成可提交的状态

  >  git add . 就是添加所有的文件

- 使用  git commit f提交文件 提交的文件必须处于可以提交的状态

  >  一般使用的是git commit -m “message”的形式来提交

## 3.工作区和暂存区

- 工作区就是电脑中能看到的目录，也就是你正在工作的目录
- 暂存区：stage，就是你在工作区中作出一些修改之后，可以通过add指令将工作区的文件放到暂存区中，只有暂存区的文件可以通过git commit来提交
- 提交到的位置叫做master分支，这是你在创建仓库时git自动为你创建的一条分支

## 4. 日志和修改

- 使用git log可查看修改日志

- 使用 git diff可以显示unstaged 状态的文件与上一次commit的文件的不同

  如果要查看已经add但未提交的文件与上一次提交的文件的不同，需要使用 git diff --cached 理查看
  同时查看staged状态和untagged状态的diff ，要使用git diff HEAD来查看

- 版本回退

  git中用HEAD表示当前的版本

  上个版本是HEAD^ 
  上上个是HEAD^^
  上一百个版本是HEAD～100

- git reset --hard HEAD^ 可回退到上个版本

- git reset --hard  commit id 可以回退到任意你能找到ID的版本，ID没必要写全，能分辨出来就好

- git reflog 可以查看你的git的每一次操作，使用这个功能可以查看未来版本的commit id

- git checkout --filename命令可以将工作区的内容回到最近一次git commit或者git add时的状态

- 使用git reset HEAD filename可以将暂存区的文件回退到工作区

- 删除文件

  使用git rm 可以删除一个文件，删除之后再commit

## 5. 分支管理

- 创建与合并分支：创建一个新的分支之后生成一个新的分支指针，这个指针指向新的分支，然后进行修改，如果需要合并，只需要将master指针指向你新建的分支的指针就好了

- 合并之后可以删除分支

- git branch dev

  git checkout dev  /git switch dev
  代表创建一个新分支dev并切换到dev分支
  git checkout -b dev，-b参数表示创建并切换
  也可以用git switch -c dev 创建并切换一个新分支

- git branch 查看所有分支

  当前分支前有一个*号

- git merge dev 将dev分支合并到master分支

  git log --graph可以看到命令分支图
  加上 no-ff参数可以做普通合并，即能看到合并的历史，而默认的fast-forward模式合并之后看不出来做过合并

- git branch -d dev 合并之后就可以删除分支了

- bug分支：使用git stash命令可以将当前工作现场隐藏起来，等修复bug之后再继续工作。

  git stash list可以查看隐藏的工作现场被隐藏到哪里去了
  使用git stash apply可以恢复，再使用git stash drop来删除stash内容
  上述两步也可以用git stash pop来一步实现
  修复之后可以用git cheer-pick <commit>命令将master上修好bug提交的修改复制到当前工作分支上

- 多人协作：git remote -v 可查看远程库信息

git push origin branch_name可以推送本地分支，如果失败则用git pull 抓取之后再push
建立本地分支和远程分支的关联：git branch --set-upstream branch_name origin/branch_name
## 6. 远程仓库

- 登陆GitHub，创建一个新的仓库

- 然后根据GitHub的提示，在本地的仓库下运行一些命令，将自己本地仓库和GitHub仓库关联

  git remote add origin git@github.com:userid/reponame.git

- 将本地仓库推送到GitHub上

  git push origin master
  注意第一次推送一个仓库时，加上 -u参数
  在第一次使用git push 或者git clone功能时，会收到一个SSH警告，输入yes回车即可，代表已经把GitHub的key添加到本机的一个信任列表里了

- git clone git@github.com:userid/reponame即可将远程库克隆到本地上

  git不仅仅支持ssh协议，也支持https协议，但是相对来说ssh协议的速度是最快的。

- GitHub
  - fork：在自己的账号下克隆了一个仓库
  - git clone git@github.com:userid/reponame.git
  - 使用pull request给官方仓库来贡献代码