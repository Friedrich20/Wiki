### 本地库
- 初始化一个Git仓库  
     `git init`  
- 添加文件到Git仓库  
     `1. git add <file> (可反复多次使用，添加多个文件)`  
     `2. git commit -m <message>`
- 查看工作区状态  
    `git status`
- 查看文件修改内容  
    `git diff <file>`
- 查看提交历史（回到过去版本）  
    `git log`
- 查看命令历史（前往未来版本）  
    `git reflog`
- 回退版本  
    `git reset --hard <commit_id OR head<>>`
- 撤销修改  
    `git checkout -- <file>`
- 删除文件  
    `git rm <file>`
    `git commit -m <message>`

### 远程库
- 关联一个远程库  
     `git remote add origin git@server-name:path/repo-name.git`  
     `e.g. git remote add origin git@github.com:michaelliao/learngit.git`
- 第一次推送master分支的所有内容，-u关联本地库和远程库   
     `git push -u origin master`
- 推送修改  
     `git push origin master`
- 从远程库克隆到本地库  
     `e.g. git clone git@github.com:michaelliao/gitskills.git`

### 分支管理
- 查看分支  
     `git branch`
- 创建分支  
     `git branch <name>`
- 切换分支  
     `git checkout <name>或者git switch <name>`
- 创建+切换分支  
     `git checkout -b <name>或者git switch -c <name>`
- 合并某分支到当前分支  
     `git merge <name>`
- 删除分支  
     `git branch -d <name>`

### generate SSH Key  
    ssh-keygen -t rsa -C "youremail@example.com"

***
### Cheatsheet
[git-cheatsheet](https://github.com/Friedr1ch/Wiki/blob/master/images/git-cheatsheet.pdf)