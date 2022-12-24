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

```bash
git add .
git add a.txt
git add -p a.txt #只add文件中的一部分，会跳出交互让我选
```

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
git branch branch_name commitid #创建新分支，且以某个commitid为版本
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
git merge master #别人修改了主分支，就要及时拉到本地并更新自己的开发分支，可能会冲突，需手动解决，解决完commit一下再merge就没冲突了
git merge --abort #如果merge出了冲突，可以undo
```

## stash

如果在本分支上修改了但是没有 commit，如果想切换分支会导致丢失本次修改，用 stash 可以让修改不丢

## reset

回退

```bash
git reset #回退add操作
git reset HEAD~1 #回退一次commit，HEAD~1指向最近的一次commit
git reset commitid #回退到这一次commitid 之后所有的修改都还在不过是unstaged(通过git log可以找到commitid)
git reset --hard commitid #把commitid以后的代码全remove了
```

## fork

把别人的项目照搬到我自己的名下，可以是为了以后 PR，可以是为了自己 DIY 功能

## rebase

类似于 merge，但是合并的步骤不同（rebase 会修改当前 commit 的父节点，而 merge 是新增一个 commit）  
如果已经 push 了就不要 rebase 了，rebase 用于清理本地 commit 历史  
**交互式 rebase**(仅用于本地)：

```bash
git rebase -i HEAD~1 #交互式编辑最新的commit
```

## Pull Request

invites reviewers to provide feedback before merging. / contribute code to other repo

## cherry pick

integrate commit(not used often)仅用于 commit 到了错的 branch 上的时候

## reflog

recover deleted commits or branches
