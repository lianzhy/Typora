#### Git版本库
1、使用当前目录作为Git仓库,初始化：  

    $ git init
该命令会生成.git目录。  
2、git clone(从现有的Git仓库中拷贝)  

    $ git clone <repo>  
    $ git clone <repo> <directory>
repo:Git仓库。directory:本地目录。  
3、配置  
git的设置使用git config命令。

    显示当前git配置信息
    $ git config --list
    编辑git配置文件
    $ git config -e (当前仓库)
    $ git config -e --global (所有仓库)
#### Git基本操作
Git常用的是以下6个命令：git clone、git push、git add、git commit、git checkout、git pull
![git-command](https://s2.loli.net/2022/05/04/SuQ5syAJWBkVEtR.jpg)

```
workspace：工作区    staging area：暂存区/缓存区  
local repository：版本库或本地仓库    remote repository：远程仓库
```
###### 操作步骤:
    $ git init    -初始化仓库
    $ git add .   -添加文件到暂存区
    $ git commit  -将暂存区的内容添加到仓库中
|命令|说明|
|:---|:---|
|git add|添加文件到仓库。|
|git status|查看仓库当前的状态，显示有变更的文件。|
|git diff|比较文件的不同，即暂存区和工作区的差异。|
|git commit|提交暂存区到本地仓库。|
|git reset|回退版本。|
|git rm|删除工作区文件。|
|git mv|移动或重命名工作区文件。|
|git log|查看历史提交记录。|
|git blame <file>|以列表形式查看指定文件的历史修改记录。|
|git remote|远程仓库操作。|
|git fetch|从远程获取代码库。|
|git pull|下载远程代码并合并。|
|git push|上传远程代码并合并。|
###### Git分支管理
创建分支：  

    $ git branch (branchname)
    创建并切换到该分支
    $ git checkout -b (branchname)
合并分支：  

    $ git merge
删除分支：
    
    $ git branch -d (branchname)
###### Git查看提交历史

    $ git log (-oneline 查看历史记录的简洁的版本 --graph 拓扑图选项 --reverse 逆向显示日志...)
    $ git blame <file>
###### Git标签    
发布版本时：

    $ git tag -a <tagname> -m "注释信息"
    查看标签信息
    $ git tag
查看当前配置的远程库

    $ git remote
提取远程库

    1、从远程库下载新分支和数据
    $ git fetch [alias]
    2、合并到当前分支
    $ git merge [alias]/[branch]
推送到远程库

    $ git push [alias] [branch]
删除远程仓库

    $ git remote rm [别名]

![img](https://s2.loli.net/2022/05/04/ifTQWDHYFctu5vh.jpg)
