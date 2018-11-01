# Git

Git是一个开源的分布式版本控制系统，用于敏捷高效地处理任何或小或大的项目。

Git的更多资料请自行百度。

## 下载安装

- [下载地址](https://www.git-scm.com/download/)

下载后安装即可。

## SourceTree

不要再说什么命令行比图形化好。（碰见过很多用命令行的人遇见冲突删全部代码。。。）

推荐一款Git图形化客户端。

[官方地址](https://www.sourcetreeapp.com/)

建议使用英文，以便对应git命令。

## Git注意事项

### 分支合并

Git官方推荐分支合并时使用--no-ff，此命令会保留原分支合并轨迹（即便源分支已删除）。大体代码如下：

```
git checkout master
git checkout -b featrue
git add 
git commit 
git checkout master
git pull 
git merge --no-ff featrue
git push
```

### 高版本修复低版本

通常Git仓库会存在多个版本，而我们经常会遇到在高版本修复一个bug后同步到低版本。

此时我们应该使用--cherry-pick(采摘樱桃)命令，cherry-pick命令允许我们将某一次提交应用到其他分支。
大体代码如下：

```
git checkout 2.0.0
git checkout -b fix2.0
git add
git commit
git push
git checkout 1.0.0
git cherry-pick <COMMIT-ID>
git push

```
