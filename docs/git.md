# git

* 设置用户信息
```
git config --global user.name "jack"
git config --global user.email "jack@example.com"
```

* 检查配置信息
```
git config --list
```

* 克隆git项目
```
git clone http://username:password@`git_address`.git
```

* 初始化仓库
```
git init
```

* 添加远程仓库
```
git remote add [remote-name] [remote-address]
git remote add origin [git_address]
git remote set-url origin [git_address]
```

* 查看git远程地址
```
git remote -v
git remote set-url origin ''
```

* 提交文件到暂存区
```
git add .
```

* 提交更新, -a 表示跳过使用暂存区域
```
git commit -m [-a] "Initial commit"
```

* 重新提交
```
git commit --amend
```

* 移除/重命名远程仓库
```
git remote rm [remote-name]
git remote rename [remote-name] [new-remote-name]
```

* 从远程仓库中抓取与拉取，fetch 不会自动merge
```
git fetch
git pull  (git pull = git fetch + git merge)
```

* 创建分支
```
git branch <local-branch> <target-branch>

git branch master oring/master
```

* 切换分支
```
git checkout <local-branch>
git checkout -b <local-branch> <target-branch>
git checkout -B <local-branch> <target-branch>

git checkout -B master origin/master
```

* 推送到远程仓库
```
git push [-f] <远程主机名> <local-branch>:<remote-branch>

git push origin feat_01:master
```

* 分支合并
```
git merge <local-branch> <target-branch>

git merge feat_01 origin/master
```


* 重定基
> rebase 命令将提交到某一分支上的所有修改都移至另一分支上
```
git rebase <upstream-branch-name> <to-branch-name>

git rebase origin/master feat_01

git rebase 出现冲突后，可以手动编辑冲突文件，冲突解决完之后，使用  git add <file-name> 来标记冲突已解决，最后执行 git rebase --continue继续
```


* 合并commit
```
git rebase -i HEAD~3
```


* cherry-pick
1. 单独合并一个提交
```
git cherry-pick <commit id>
```
2. 不包含 start-commit-id 的提交cherry-pick到当前分支
```
git cherry-pick <start-commit-id>..<end-commit-id>
```
3. 当cherry-pick时，没有成功自动提交，这说明存在冲突，解决冲突后，使用  git add <file-name> 来标记冲突已解决，最后执行git cherry-pick --continue继续



* git ssh
```
ssh-keygen -t rsa -C "your email"
rsa为密钥昵称，双引号里是输入email地址
在ssh文件夹下有两个文件，一个是私钥，以pub结尾的为公钥，把公钥添加到gitlab中的ssh密钥中
回到git窗口，把私钥加入到ssh中
ssh-add id_rsa
(Could not open a connection to your authenticatoin agent 不影响)

TortoiseGit设置SSH
TortoiseGit的SSH客户端 D:\TortoiseGit\bin\TortoiseGitPlink.exe
改成win下git默认的 D:\Git\usr\bin\ssh.exe
```
