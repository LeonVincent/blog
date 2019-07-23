```
git log //只查看当前分之
git log --all //查看所有的分之
git log --oneline
git log -n3 //查看三行

git branch -v //看本地有多少分之
git last //查看上一次的commit

```
### 13.进一步理解HEAD和branch
```
1.git checkout -b fix_readme fix_css// 最后一个属性代表这个fix_readme分支是基于fix_css 创建de
2.
git diff commit1 commit2 //比较两个commit的差异

git diff HEAD HEAD^ //比较现在的commit 和上一个commit 的差异 ^表示父亲 
//父亲的父亲可为 HEAD^^
//或者HEAD~n n为几就是向前几个HEAD

```

### 14. 删除不需要的分支
```
git branch -d 分支名
//-d如果删除不了，可以用-D

```
### 15. 怎么修改最新commit的message

```
git commit --amend// 后面是vim命令 w: 写入 q: 退出
```

### 16. 怎么修改老旧的commit的message

```
git rebase -i commit_old // -i 交互式
//commit_old 指的是要变更的commit的前一个commit 输入命令后会弹出两次编辑面板，这时候根据说明来就可以了

```

### 17.怎么把连续的多个commit整理成一个

```
git rebase -i commit_n //commit_n :要合并哪几个，只需要把合并的几个commit的第一个commit 的前一个commit 写上

```
![image](https://zhitu.isux.us/assets/img/imgTest/IMG20190525/20190525174523-K4S1Uq5Fx7.png)

### 18.怎么把间隔的几个commit整理成1个

```
git rebase -i commit_n //commit_n :要合并哪几个，只需要把合并的几个commit的第一个commit 的前一个commit 写上

i 弹出来的编辑页面可以编辑它的顺序
```

### 19.怎么比较暂存区和HEAD所含文件的差异
```
git diff --cached
```

### 20.怎么比较工作区和暂存区所含文件的差异

```
git diff //直接git diff 可查看所有的工作区和暂存区的差异

git diff -- readme // -- (空格) 后面跟哪个文件就查看哪个文件工作区和暂存区之间的差异

```

### 21.如何让暂存区恢复成和HEAD的一样

```
git reset HEAD// 所有暂存区的文件都会恢复成和HEAD一样

git reset HEAD -- index.html//恢复单个的文件
```

### 22.如何让工作区的文件恢复为和暂存区一样
```
git checkout -- index.html//
```

### 24.消除最近的几次提交
```
git reset --hard _commit //_commit 要退回的最早一个commit的前一个
git reset --hard HEAD //回退到上一个版本
```
### 25.查看一个文件在两个不同的分支中的差异

```
git diff branch1 branch2 -- index.html
```
### 26.正确删除文件的方法

```
rm index.html //工作区删除index.html
git rm index.html //删除暂存区的index.html 可直接用这个方法，不用第一个方法
```
### 27.开发中临时加塞了紧急任务怎么办
```
git stash 
git stash apply 或者 git stash pop
apply 保留着git stash list 里面的信息
```

```
git diff: 对比工作区(未 git add)和暂存区(git add 之后)
git diff --cached: 对比暂存区(git add 之后)和版本库(git commit 之后)
git diff HEAD : 对比工作区(未 git add)和版本库(git commit 之后)
git checkout -- readme.txt ： 改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，版本库里的版本替换工作区的版本，无论工作区是修改还是删除，都可以“一键还原”。
git reset HEAD <file> : 文件添加到了暂存区后丢弃修改的命令
git rm <file>: 删除文件
```



























