# git操作指令笔记
- git init 初始化一个仓库
- git add <file> 添加到暂存区,可反复多次使用，添加多个文件
- git commit -m 'some log'上传到本地仓库（当前分支）
- git status 查看仓库当前的状态，告诉你是否被修改
- git diff 查看difference，具体修改了什么内容
- git log 命令显示从最近到最远的提交日志
- HEAD指向的版本就是当前版本，因此，Git允许我们在版本的历史之间穿梭，使用命令git reset --hard commit_id。
- 穿梭前，用git log可以查看提交历史，以便确定要回退到哪个版本。
- 要重返未来，用git reflog查看命令历史，以便确定要回到未来的哪个版本。
- git push 把本地库的内容推送到远程
```
Git鼓励大量使用分支：

查看分支：git branch

创建分支：git branch <name>

切换分支：git checkout <name>

创建+切换分支：git checkout -b <name>

合并某分支到当前分支：git merge <name>

删除分支：git branch -d <name>

git config -l 查看config配置

创建SSH
$ ssh-keygen -t rsa -C "your_email@youremail.com"

局部设置git
git config --local user.name "你的名字"
git config --local user.email "你的邮箱"
```
...