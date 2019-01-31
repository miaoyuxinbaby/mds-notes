# git操作指令笔记


## sourcetree在github密码修改后，要删除旧仓库重新拉代码，才能保证新ssh生效。恶心！

- git init 初始化一个仓库
- git add `<file>` 添加到暂存区,可反复多次使用，添加多个文件
- git commit -m 'some log'上传到本地仓库（当前分支）
- git status 查看仓库当前的状态，告诉你是否被修改
- git diff 查看difference，具体修改了什么内容
- git log 命令显示从最近到最远的提交日志
  - HEAD指向的版本就是当前版本，因此，Git允许我们在版本的历史之间穿梭，使用命令git reset --hard commit_id。
  - 上一个版本就是HEAD^，上上一个版本就是HEAD^^，当然往上100个版本写100个^比较容易数不过来，所以写成HEAD~100。
- 穿梭前，用git log可以查看提交历史，以便确定要回退到哪个版本。
- 要重返未来，用git reflog查看命令历史，以便确定要回到未来的哪个版本。
- git push 把本地库的内容推送到远程

```html
Git鼓励大量使用分支：

查看分支：git branch

创建分支：git branch <name>

切换分支：git checkout <name>

创建+切换分支：git checkout -b <name>

合并某分支到当前分支：git merge <name>

删除分支：git branch -d <name>

存档现场 git stash (soucetree 贮藏)

查看存档 git stash list 

读取存档 git stash apply + git stash drop 或 git stash pop

把文件在工作区的修改全部撤销 git checkout -- 文件名

查看对应的远程仓库版本（fetch和push地址） git remote -v

打一个新标签 git tag <name>

可以指定标签信息 git tag -a <tagname> -m "blablabla..."

查看标签 git tag

查看标签信息 git show <tagname>

删除一个本地标签 git tag -d <tagname>

可以推送一个本地标签 git push origin <tagname>

推送全部未推送过的本地标签 git push origin --tags

删除一个远程标签 git push origin :refs/tags/<tagname>

git config -l 查看config配置

创建SSH
$ ssh-keygen -t rsa -C "your_email@youremail.com"

局部设置git
git config --local user.name "你的名字"
git config --local user.email "你的邮箱"

##　HTTP请求gitlab时需要输入帐号密码： 偷懒方案(建议使用ssh)

在项目的 .git/config 里修改[remote "origin"]里的url <br>
将原来的http://gitlab.*****.com修改为http://username:password@gitlab.*****.com

## 关于 commit pull的顺序。

当没有别人和你修改同一个文件时，先pull，再commit,这样不会出现多余的merge信息，

如果有人和你一起改同一个文件，那就先commit，再Pull，防止自己代码被覆盖。