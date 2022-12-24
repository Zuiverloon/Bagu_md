# GIT

version control system where we can track code changes.  
**github**:onlien website that hold all projects  
**Repository**: project/where project is kept  
**.git folder(hidden folder)**:store all changes recorded in the history of this project

## What is branching?

In main branch, we cannot see any changed made in featured branch. We can write new code without modifying the main branch.

## Clone

bring a repo hosted elsewhere to my local machine

## add

track files and changes in Git

## commit

save files in Git

```bash
git commit -am "message" #如果所有修改的文件都不是新建的，可以这样写而不用手动add
```

## push

upload Git commits to a remote repo like github

## pull

download changes from remote repo to the local machine, opposite to push

## branch

show all branches

```bash
git branch # 查看所有分支
git branch -d feature1 # 删除feature1分支
```

## checkout

switching between branches

```bash
git branch
git branch -b newbranch # 新建分支
```

## diff

show what changes have been made and it compares two versions of the code

```bash
git diff feature-1 # 对比当前分支和feature-1分支
```

## merge

合并某个分支代码到当前分支

```bash
git merge feature-1 # 合并feature-1分支到当前分支
git merge master #别人修改了主分支，就要及时拉到本地并更新自己的开发分支，可能会冲突，需手动解决
```

## stash

如果在本分支上修改了但是没有 commit，如果想切换分支会导致丢失本次修改，用 stash 可以让修改不丢
