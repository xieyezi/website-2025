---
title: git-flow流程
---

Gitflow 是一个基于 feature 分支管理的版本发布方案。它是由荷兰程序猿 Vincent Driessen 设计研发，开源项目地址 gitflow-avh。
大致流程是：

- 不同 feature 在不同 feature 分支上单独开发(或测试)。
- 确定版本号和此版本将要发布的功能后，将相应 featustre 分支统一向 develop 分支合并，然后拉出新的 release 预发布分支。
- release 分支作为持续集成关注的分支，修复 bug。
- 待 release 分支测试验收通过后，统一向 master 分支和 develop 分支合并，并在 master 分支打 tag。
- 根据 tag 发布 apk 版本。

若线上发现严重 bug，需走 hotfix 流程。

- 基于 master 分支拉出 hotfix 分支修复线上问题。
- bug 修复完成统一向 master 和 develop 分支合并。
- master 分支打上新的 tag，发布新版本。

下面将介绍如何使用 Gitflow 命令完成上述版本发布，一条 Gitflow 指令可能对应了一系列 git 命令，为的是规范化开发流程，提高代码管理效率。

### Mac 平台安装

```
brew install git-flow
```

brew 表示 Homebrew，它是 mac 平台常用的包管理器，没有安装则需先安装，安装可参考 Mac OS 下 brew 的安装和使用。

### 初始化

先将远程仓库克隆到本地。

```
git clone <project_url>
```

各种初始化 Gitflow 配置。

```
git flow init
```

命令行会提示你是否修改 Gitflow 提供的默认分支前缀。不同场景的分支前缀不同，默认情况下分支前缀是这样的：
场景 | 分支前缀
-- | --
新功能 | feature
预发布 | release
热修复 | hotfix
打 tag | 空

分支前缀的作用是区分不同分支的使用场景，同时当你使用 Gitflow 命令时就不需手动添加分支前缀了，Gitflow 会帮你加上。
比如开发新功能需创建一个 feature 分支，名为 gitworkflow，使用下面的代码将会创建一个名为 feature/gitworkflow 本地分支。

```
git flow feature start gitworkflow
```

通常情况下不需要修改默认的命名前缀，只需加上-d 就可跳过修改命名阶段。

```
git flow init -d
```

### 行为/Action

通常来说，一种场景的完整生命周期应至少包含以下几种行为：

- start 开始开发
- publish 发布到远程分支
- finish 完成开发、合并到主分支

我们首先以 feature 场景为例，看看如何完成工作流。

### feature 流程

#### start

新功能开始开发前，需准备好开发分支。

```
git flow feature start <feature_name>
```

执行上面的命令将会在本地创建名为<feature_name>的分支，并切换到该分支，而且不论当前所处哪个分支都是基于 develop 分支创建，相当于执行了下面的 git 的命令。

```
git checkout -b feature/<feature_name> develop
```

需要注意基于的是本地的 develop 分支，执行此命令前一般需要拉取最新的远程代码。

#### publish

在本地开发完成新功能并 commit 后，需要将本地代码 push 到远程仓库。

```
git flow feature publish <feature_name>
```

这行指令做了三件事。

- 创建一个名为 feature/<feature_name>的远程分支。
- 本地分支 track 上述远程分支。
- 如果本地有未 push 代码，则执行 push。

转换成 git 命令就是下面的样子：

```
git push origin feature/<feature_name>
git push --set-upstream origin feature/<feature_name>
git push origin
```

注意：如果已经执行过 publish 后又有新的代码需 push，再次执行将报错，因为它始终会试图创建一个远程分支。此时需执行正常的 push 命令 git push origin。

#### finish

当功能开发完毕后就将进入测试阶段，此时需将一个或多个 feature 分支统一合并到 develop 分支。

```
git flow feature finish <feature_name>
```

这行指令也做了三件事。

- 切换到 develop 分支。
- 合并代码到 develop 分支
- 删除本地 feature/<feature_name>分支。
  等价于执行下面的 git 命令：

```
git checkout develop
git merge feature/<feature_name>
git branch -d feature/<feature_name>
```

如果 merge 时发生了冲突，则在第二步 merge 时终止流程，即不会再删除本地分支。但当前已处于 develop 分支，待本地冲突解决并 commit 后，重新执行`git flow feature finish <feature_name>`即可完成 finish 流程。
细心的同学可以已经发现 finish 还有两件事没做。

- develop 分支代码还未 push。
- 未删除远程分支 feature/<feature_name>。

也就是还需执行

```
git push origin develop
git push origin :feature/<feature_name>
```

### release 流程

当新功能开发完毕，将进入测试阶段，此时需要基于 develop 分支拉出 release 分支进行集成测试，也有将 release 场景作为预发布环境进行测试的，即 feature 场景已完成常规测试，在这种情况下，一般而言 release 只有少数改动。在这里我们先不讨论项目流程问题。
使用 start 指令开启一个 release 场景，通常以版本号命令，我们以 v2.0 为例：

```
git flow release start v2.0
```

此命令会基于本地的 develop 分支创建一个 release/v2.0 分支，并切换到这个分支上。
为了让其他协同人员也能看到此分支，需要将其发布出去。

```
git flow release publish v2.0
```

以上和 feature 场景十分类似。
待测试通过需要发布正式版：

```
git flow release finish v2.0
```

这一步做的动作有点多，大致是：

- git fetch
- release/v2.0 分支代码向 master 合并。
- 生成名为 v2.0 的 tag。
- release/v2.0 分支代码向 develop 合并。
- 删除本地 release/v2.0 分支。
- 切换回 develop 分支。

如果 merge 产生冲突不会终止流程，只是不会将本地的 release 分支删除，待解决完冲突后需再次执行 finish 指令。
另外需要注意的是，如果本地还有未 finish 的 release 分支，将不允许使用 start 指令开启新的 release 分支，这一点是对并行发布的一个限制。
release finish 只是完成了本地代码的一系列操作，还需要同步到远程仓库。

```
git push origin develop
git push origin master
git push origin v2.0
```

或者使用下面的命令推送所有的分支和 tag。

```
git push origin --all
git push origin --tags
```

### hotfix 流程

当 tag 打完，也表示正式版本发布出去了，如果此时在线上发现了严重的 bug，需要进行紧急修复。
此时我们假设版本号变为 v2.0-patch。

```
git flow hotfix start v2.0-patch
```

这将创建一个 hotfix/v2.0 本地分支并切换到该分支。 hotfix 没有 publish 指令，认为 hotfix 应该是个小范围改动，不需要其他协同人员参与。
待本地修改结束 commit 后，执行 finish 指令。

```
git flow hotfix finish v2.0-patch
```

按照 Gitflow 工作流，它会执行下面的任务，与 release 基本一致。

- git fetch
- hotfix/v2.0-patch 分支代码向 master 合并。
- 生成名为 v2.0-patch 的 tag。
- hotfix/v2.0-patch 分支代码向 develop 合并。
- 删除本地 hotfix/v2.0-patch 分支。
- 切换回 develop 分支。
