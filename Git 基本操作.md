# Git知识点
### Git的基本操作

#### 获取与创建项目

git init    
在目录中创建新的Git仓库

mkdir gittest  ------>创建gittest文件夹 

cd gittest    
进入gittest文件夹。

git init    
此时目录中讲生成.git子目录。

git clone    
拷贝一个Git仓库带本地

---

#### 传代码的基本步骤

git status    
查看项目当前状态，如果有修改等操作会将文件列出来。

git add_file_ file为要缓存的文件    
将文件添加到缓存。

再次使用git status进行查看。

git diff    
查看改动内容。

git commit  
将缓存内容添加到仓库中。

---

#### 分支管理
git branch -a    
列出所有分支。

git branch newBranch  
创建新的分支。

git branch -d  
删除分支

git merge  
合并分支。

如果合并分支冲突解决完冲突后使用git add 冲突文件，说明冲突已解决。 






