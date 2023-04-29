---
title: Learning Git
author: fanyixuanf
date: 2023-04-29 12:00:00 +0800
categories: [Tutorial]
tags: [Git]
---

### git基本配置
```markdown 
  git config --global --list
  git config --local --list
  git config --global user.name "your name"
  git config --global user.email "your email"
  git config --local user.name "your name"
  git config --local user.email "your email"
```

### 删除配置信息
- ``` git config --unset --global 要删除的配置项 ```
- ``` git config --unset --local 要删除的配置项 ```

### git ssh
- ``` ssh-keygen -t rsa -C “your email”```
- ``` cat ~/.ssh/id_rsa.pub ```
- 填入github ssh

### 缓存仓库(repository)的用户名和密码
- ``` git config --global credential.helper cache ```
- ``` git config --global credential.helper 'cache --timeout=3600' ```
- ``` git config --global credential.helper store ```

### 命令分组

| Basic Commands  | Undo Commits | Combine Branches |     Remote Server      |
|:---------------:|:------------:|:----------------:|:----------------------:|
|   git commit    |  git reset   |    git merge     |       git fetch        |
|   git branch    |  git revert  |    git rebase    |        git pull        |
|  git checkout   |              |                  |        git push        |
| git checkout -b |              |                  |        git tag         |

### 克隆所有
- ```  git clone --recursive git@github.com:fanyixuanf/learningGit.git```
- ``` git submodule update --init --recursive  ```

### 查看哪些文件没被 Git 管控
- ``` git ls-files --others ```

### 创建branch
- ```git checkout -b dev origin/main```
- ``` git checkout -b dev2 origin/main ```

### 删除远程branch
- ``` git push --delete origin dev2 ```
- ``` git push remote :远端分支名```

### 删除远端 origin 已不存在的所有本地分支
- ``` git remote prune orign ```

### 删除本地branch
-  ```  git branch -D dev2  ```
-  ``` git branch -d dev2 ```

### 删除已合并到 master 分支的所有本地分支
- ``` git branch --merged master | grep -v '^\*\| master' | xargs -n 1 git branch -d  ```



### 从远程拉取(fetch) 所有分支
- ``` git fetch --all ```

### 从远程拉取一个分支
- ``` git checkout --track origin/dev ```
- ``` --track ``` 是 ```git checkout -b [branch] [remotename]/[branch]``` 的简写


### 我从错误的分支拉取了内容，或把内容拉取到了错误的分支
- ``` On branch dev2 ```
- ``` git pull origin dev ``` 拉取错误的分支
  - ``` git reflog ```
    - ``` 6be641e (HEAD -> dev2, origin/dev2) HEAD@{0}: checkout: moving from dev to dev2 ```
  - ```  git reset --hard 6be641e ```
  - ```  git reset --hard HEAD@{0} ```

### 关联远程分支
- ``` 
  //可以先通过下面命令刷新远端分支列表
  git remote update origin --prune
  //更换关联的远端分支
  git branch --set-upstream-to=origin/dev dev
  ```

### 扔掉本地的提交，本地分支与远程的保持一致
- ```
  git reset --hard origin/dev dev 
  ```

### 要提交到一个新分支，但错误的提交到了master或者其他分支
- ```
   // 新建一个分支, 不切换分支
   git branch dev1
   // 当前分支重置到前一个提交
   git reset --hard HEAD^ 
   // or
   git reset --hard SHACommit
   // 切换到dev1
   git checkout dev1
  ```

### dev1分支拿文件到dev， dev1领先dev
- ``` 
  On branch dev
  git checkout dev1 -- fileName.x
  ```

### 几次提交应该在提交到不同的分支，结果都提交在main上
- ```
  // 查看log 
  git log
  // 把main分支重置到正确的commit上
  git reset --hard SHACommit-main
  // 创建不同的分支为不同的提交(dev)
  git checkout -b dev
  git cherry-pick SHACommit-dev
  // 切换到main分支，创建dev2分支
  git checkout main
  git checkout -b dev2
  git cherry-pick SHACommit-dev2
  ```

### 删除上游(upstream)分支被删除了的本地分支
- ``` git fetch -p  ```

### 不小心删除了本地分支
- ``` 
  git reflog
  git checkout -b dev
  git reset --hard SHACommit 
  ```




### 显示当前 ```HEAD```上的最近一次的提交(commit)
- ``` git show ```
- ``` git log -n1 -p  ```

### 修改提交信息commit message
- 未push
  - ``` git commit --amend --only ```
- 已push
  - 修改commit message后 ```force push```
    - ``` git commit --amend --only ```
    - ``` git push origin dev --force ```
    - ``` git push --force-with-lease origin dev ```

### 单次提交里的用户名和邮箱不对
- ``` git commit --amend --author "schadenfreude <schadenfreudef@gmail.com>" ```

### 从一个提交里移除一个文件
- ``` 
  git checkout HEAD^ dev2/dev2.md
  git add -A
  git commit -m ""
  git push origin dev
  ```
- ``` 
  git checkout HEAD^ dev2/dev2.md
  git add -A
  git commit --amend 
  git push origin dev
  ```

### 删除最后一次提交
- 已push
  - 不到万不得已的时候不要这么做
  ```
     git reset HEAD^ --hard 
     git push -f
  ```
  - 推荐
  ``` 
  git revert SHA of commitid
  ```
- 未push
  - ```
    git reset --soft HEAD@{1} 
    ```
### 删除任意提交
- 不到万不得已的时候不要这么做
  - ``` 
    git rebase --onto SHA_OF_BAD_COMMIT^ SHA_OF_BAD_COMMIT  
    git push -f   
    ```
- 推荐
  - ``` git revert SHA of commitid ```

### 尝试push一个修正后的提交(amended commit)到远程，但是报错：
- *要避免强推*
- ``` 
  To github.com:fanyixuanf/learningGit.git
  ! [rejected]        dev -> dev (non-fast-forward)
  error: failed to push some refs to 'github.com:fanyixuanf/learningGit.git'
  hint: Updates were rejected because the tip of your current branch is behind
  hint: its remote counterpart. Integrate the remote changes (e.g.
  hint: 'git pull ...') before pushing again.
  hint: See the 'Note about fast-forwards' in 'git push --help' for details.
  ```
  - ``` git push origin dev -f ``` or ``` git push origin mybranch --force  ```

### 做了一次hard reset后，想找回这次提交
- ``` 
  git reflog
  git reset --hard SHA1234
  ```

### 提交文件的一部分，而不是这个文件的全部
- ```  git add --patch filename.x  ``` or ``` git add -p filename.x ```
- ``` git add -N filename.x ```
- ``` git add -p ```

### 把暂存的内容变成未暂存，把未暂存的内容暂存起来
- ``` 
   git commit -m "WIP"  
   git add .  
   git stash  
   git reset HEAD^  
   git stash pop --index 0
  ```

### 把未暂存的内容移动到一个新分支
- ``` git checkout -b dev1 ```

### 把未暂存的内容移动到另一个已存在的分支
- ```
   git stash  
   git checkout dev
   git stash pop
  ```

### 丢弃本地未提交的变化
- ```
  # one commit  
  git reset --hard HEAD^
  # two commits
  git reset --hard HEAD^^
  # four commits
  git reset --hard HEAD~4
  # or
  git checkout -f
  ```

### 丢弃某些未暂存的内容
- ``` git checkout -p
  git checkout -p
  # or
  git stash -p
  git reset --hard
  git stash pop
  # or
  git stash -p
  git stash drop
  ```
### 撤销rebase/merge
- ``` 
  // Git 在进行危险操作的时候会把原始的HEAD保存在一个叫ORIG_HEAD的变量里
  git reset --hard ORIG_HEAD
  ```

### 已经rebase过, 但是我不想强推(force push)
- ```
   // main 
   git checkout dev
   // dev
   git rebase -i main
   git checkout main
   // main 
   git merge --ff-only my-branch
  ```

### 组合几次提交(commit)
- ``` 
  // on branch dev
  git reset --soft main  
  git commit -am "xxxxx"
  // or 
  git rebase -i main
  // or 
  git rebase -i HEAD~2
  
  ```

### 安全merge
- ``` git merge --no-ff --no-commit dev ```
- 合并成一次提交``` git merge --squash dev ```
- 有几个正在进行的提交(commit)。这时候不希望把已经推(push)过的组合进来，因为其他人可能已经有提交(commit)引用它们了
  - ``` git rebase -i @{u} ```

### 检查是否分支上的所有提交(commit)都合并(merge)过了
- ``` git log --graph --left-right --cherry-pick --oneline HEAD...feature/dev ```
- ``` git log main ^feature/dev --no-merges ```

### rebase
- 继续``` git rebase --continue ```
- 结束``` git rebase --abort```

### 暂存所有
- ``` git stash  ```
- 排除文件``` git stash -u ```

### 暂存指定文件
- ``` git stash push path/filename.x```
- ``` git stash push path/filename.x path/filename1.x```

### 暂存时写入msg
- ``` git stash save <msg> ```
- ``` git stash push -m <msg> ```

### 使用某个指定暂存
- ```
  git stash list
  git stash apply "stash@{n}" 
  // or 时间线
  git stash apply "stash@{2.hours.ago}"
  ```

### 暂存时保留未暂存的内容
- ``` 
  git stash create  
  git stash store -m "commit-message" CREATED_SHA
  ```

### 改变一个文件名字的大小写，而不修改内容
- ``` git mv --force name Name  ```

### 从Git删除一个文件，但保留该文件
- ``` git rm --cached fileName.x ```

### 删除tag
- ``` 
   git tag -d <tag_name>  
   git push <remote> :refs/tags/<tag_name>
  ```

### 恢复已删除tag
- ```
  git fsck --unreachable | grep tag 
  git update-ref refs/tags/<tag_name> <hash> 
  ```

### 查看已有标签
- ``` git tag ```

### 新建标签
- ``` git tag -a v1.0 -m "msg"```

### 给指定的 commit 打标签
- ``` git tag v1.0 commitid ```

### 推送一个本地标签
- ``` git push origin v1.0 ```

### 推送全部未推送过的本地标签
- ``` git push origin --tags  ```

### 远端交互
- 查看所有远端仓库``` git remote -v ```
- 添加远端仓库``` git remote add url ```
- 删除远端仓库``` git remote remove remote的名称```
- 重命名远端仓库``` git remote rename 旧名称 新名称 ```
- 把远端分支的变更拉到本地，且 merge 到本地分支``` git pull origin 分支名 ```
- 将本地分支 push 到远端``` git push origin 分支名 ```
- 删除远端分支``` git push remote --delete 远端分支名 ```or```git push remote :远端分支名```
