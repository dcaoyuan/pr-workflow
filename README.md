# Github Pull Request Workflow

以下指南使用如下的 git alias:
```text
[alias]
	co = checkout
	br = branch
	ci = commit
	st = status
````


### 1. Issues - 管理 Features / Bugs。注：这一项不是必须的

1. 创建一个 Issue
2. 选择合适的 labels，比如 bug, feature, t:crawler (t 在这里是 Topic 的意思) 等等
3. 注意 Github 生成、给出的 Issue 号，比如 #15

### 2. 创建一个 branch，（并/可以）关联到某个 Issue

#### 2.1 分支名不妨表达为 wip-xxx-xx 的形式，这里 wip 指 Work in Progress，xxx 可以是 issue 号，或者一个简短的描述，还可以在后面继续加 -xx，作为小版本名等等

```shell
git co -b wip-xxx-xx
```

#### 2.2 编辑、修改代码等项目文件。可以随时 commit 到本地

```shell
git ci -a -m "WIP #15 Do something"
```

注意在 commit message 中

1. WIP 通常用来指“还在进行中”，告诉伙伴先不要 merge。也有人会用 [Do Not Merge]
2. #15 则会在 push 到 github 仓库后，自动关联到 Issue #15

#### 2.3 Push 该分支到 github

```shell
git push -u origin wip-xxx-xx
```

在 push 命令中加 -u (--set-upsteam) 开关，这样本地的这个分支就会跟 push 到 github 上的那个分支关联起来，也即 github 上的 wip-xxx-xx 分支将成为本地该分支的 upstream，今后 `git fetch/pull` 的时候本地这个分支会跟踪远端分支的变化（比如伙伴修改了）

如果你的 git 版本足够高，命令行的完成提示会帮助你敲对这行命令，比如，你这时直接输入：

```shell
git push
```

会反馈：
```shell
[dcaoyuan@cyblue brooks]$ git push
fatal: The current branch wip-more-seeds has no upstream branch.
To push the current branch and set the remote as upstream, use

    git push --set-upstream origin wip-xxx-xx
```
这里提示的命令就是正确的。


### 3. 将该分支提交为 Pull Request

到 Github 上找到这个分支，并将其搞成 Pull Request (PR)

你总是可以找到做这个事的按钮的，否则就是 github 的 PM 的问题了。

### 4. Review PR

你可以 @whoxxx 的方式提醒伙伴来 review

Review 的过程在 github 上有良好的 UI 支持，你可以标注某段代码、发表看法。。。

还可以把这个 PR 拉到本地（实际上是分支，对吧。记住一个 PR 总是对应 一个 Branch，但反过来则未必）

#### 4.1 在本地修改 PR

如果就是你最初提交 PR (Branch) 的，那你本地已经有了这个分支，你可以随意修改、提交和 push。（记住，一个 Branch 是可以有一个到很多个 commits 的）

如果你本地没有，可以这样拉下来：

首先，先把远端（orign）所有最新的东西取一次
```shell
git fetch origin
```

然后，切到那个需要处理的分支（或者 PR），比如
```shell
git co -b wip-xxx-xx origin/wip-xxx-xx
```

再，先试着在这个分支下将 master 的最新动态合并以下试试
```shell
git merge master 
```

如果在这里就冲突了，最好先解决它（如果各位都遵循合适的流程，这里出现冲突的机会不大）。

然后现在你就跟这个分支的原作者处在相等的地位了。请参见本节开头。

#### 4.2 合并 PR 前的准备

等一切都修改到满意，大家也没意见或者顾不上你的时候，就可以考虑将这个 PR 合并到主干了。

##### 4.3.1 如果这个 PR 只有一次提交，你可能想把注释现在改为

1. 去掉 [WIP] 头
2. 将 Issue #15 直接改为 Closed #15

也就是从这个样子：
```text
[WIP] Issue #15 Do something
```
改成这个样子：
```text
Closed #15 Done something
```
这时，可以用
```shell
git ci --amend
```
来改这个注释。

然后，因为你用了 --amend，所有在 push 时要加上 -f (--force) 开关
```shell
git push -f
```
只要不在主干里 `git push -f`，一般是不会有人骂你的，因为这些 WIP 的 branch 通常在任务完成后就可以删掉了。

另外，因为注释中现在有 `Closed #15` 的字样，所以 push 后 github 会自动把 Issue #15 改为 closed，并会在该 Issue 中记录下相应的 commit id，以后可以随时回溯问题解决的细节。

##### 4.3.2 合并前想做 Rebase ?

如果这个 PR 包含很多次 commits，你想将这些 commits 整理一下，比如合成一个 commit，或者其它的什么。

记得把 origin/master 作为 rebase 的起点就行了
```shell
git rebase -i origin/master
```
比如合并三个 commits 成一个
```
pick e7ba81d Commit-1
s 5756e15 Commit-2
s b1b8189 Commit-3
```
rebase 完成后，然后也还可以 --amend，然后，也要 push -f

#### 4.3 合并 PR 到主干

到 github 的 UI 去，相信你能找到那个按钮。

> 注：合并前或完成后，你可以在 github 上直接到这个 PR 的 review UI 里 Closed #15 也行。

然后在你本地 
```
git co master
git pull
```
这样你就切回到主干，可以准备下一个 Issue 了。

#### 4.4 可以删掉这个 PR 的 branch

然后你还可以在 github UI 直接 delete 远端的这个 branch。

也可以再在本地 delete 本地的这个 branch:
```
git br -d wip-xxx-xx
```
或者（如果它抱怨你本地的 wip-xxx-xx 没有合并过的话）
```
git br -D wip-xxx-xx
```

删掉这个 branch 并不会删掉 PR，PR 本身在 github 上也是作为 Issue 管起来的，所以之前所有 review 的信息都还在 PR 里。

### 5 怎样添加自定义的 Labels/Milestones

#### 5.1 Labels

访问跟项目相关的地址，在后面加上 `/labels`，即：
```
https://github.com/<org_name>/<project_name>/labels
```
比如：

https://github.com/lianpian/lianpian.github.io/labels

#### 5.2 Milestones

访问跟项目相关的地址，在后面加上 `/milestones`，即：
```
https://github.com/<org_name>/<project_name>/milestones
```
比如：

https://github.com/lianpian/lianpian.github.io/milestones

### 6. 将 git.xxxxx.com 上的项目迁移到 github

首先将在 github 上创建一个完全空的新项目，比如 ripple。注意不要选中 "Initialize this repository with a README"。

然后在你本地 git clone git.xxxxx.com 上的相应项目（如果本地还没有的话）

对于你本地的项目目录，比如 ripple:
```shell
vi ripple/.git/config
```
将其中的：
```
[remote "origin"]
  url = ssh://dcaoyuan@git.xxxxx.com:29418/ripple
  fetch = +refs/heads/*:refs/remotes/origin/*
```
改为：
```
[remote "xxxxx"]
  url = ssh://dcaoyuan@git.xxxxx.com:29418/ripple
  fetch = +refs/heads/*:refs/remotes/origin/*
```
也即新增一个 remote 源，这个 [remote "xxxxx"] 将来也可以完全删掉。

然后执行：
```
git remote add origin git@github.com:lianpian/ripple.git
```
将默认的 "origin" remote 源指向 github 上新建的仓库，这时 .git/config 看起来将是这个样子：
```
[core]
  repositoryformatversion = 0
  filemode = true
  bare = false
  logallrefupdates = true
[remote "wdj"]
  url = ssh://dcaoyuan@git.xxxxx.com:29418/ripple
  fetch = +refs/heads/*:refs/remotes/origin/*
[remote "origin"]
  url = git@github.com:lianpian/ripple.git
  fetch = +refs/heads/*:refs/remotes/origin/*
[branch "master"]
  remote = origin
  merge = refs/heads/master
```

最后，将有史以来的 commits 全部 push 到 github 这个新的 remote 仓库：
```
git push -u origin master
```

### Reference

https://github.com/akka/akka/blob/master/CONTRIBUTING.md

https://github.com/akka/akka-meta/issues?q=is%3Aissue+is%3Aclosed


