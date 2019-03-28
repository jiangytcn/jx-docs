---
title: 贡献代码
linktitle: 开发
description: 如何为 Jenkins X 的发展做贡献
date: 2018-05-16
publishdate: 2018-05-16
lastmod: 2018-05-16
categories: [contribute]
keywords: [dev,open source]
authors: [digitalcraftsman]
menu:
  docs:
    parent: "contribute"
    weight: 10
weight: 10
sections_weight: 10
draft: false
aliases: [/contribute/development/]
toc: true
---

## 介绍

Jenkins X 是由众多[开发者][contributors]开发的开源项目. 还有很多[尚未解决的问题][issues], 我们需要你的帮助来使Jenkins X变得更棒. 即使你不是一个Go语言的专家， 也可以对项目的开发贡献力量。


## 假设

本篇指导文档将帮助你逐步熟悉它，因此我们假定：

* 你是刚刚接触Git或者开源项目
* 你是Jenkins X的爱好者并乐于对项目的发展贡献力量

{{% note "Additional Questions?" %}}
If you're struggling at any point in this contribution guide, reach out to the Jenkins X community in [Jenkins X's Discussion forum](https://jenkins-x.io/community/).
{{% /note %}}

## 安装 Go

Go语言环境的安装仅需要几分钟. 并且多种方式可供选择

如果在安装过程当中遇到问题，请查阅 [Go Bootcamp, which contains setups for every platform][gobootcamp] 或者向Jenkins X[论坛][forums]中寻求帮助


### 从源码安装 Go

[下载最新版Go源码][godl] 并通过官方[安装文档][goinstall]进行安装.

安装完成后，确认是否一切工作正常. 打开一个新的终端或者在Windows上的命令行并输入:

```
go version
```

在终端的窗口上可以看到类似如下的信息. 注意`version`表示的是在在更新此文档时最新的Go的版本信息:

```
go version go1.8 darwin/amd64
```

下一步，确保[根据安装文档][setupgopath] 设置了 `GOPATH` 环境变量。
通过 `echo $GOPATH` 输出 `GOPATH`. 应该是指向了你的合法的Go的工作目录的非空字符串, 如:

```
/Users/<yourusername>/Code/go
```

### 使用 Homebrew 安装 Go

如果你是MacOS用户并且安装了[Homebrew](https://brew.sh/), 安装过程将会很简单, 在终端中执行以下命令:

{{< code file="install-go.sh" >}}
brew install go
{{< /code >}}

### 通过 GVM 安装 GO

更多有经验的用户使用[Go Version Manager][gvm] (GVM), GVM 允许你在`同一台机器上`安装并切换使用多种版本的GO语言环境. 如果你是初学者，可能不太需要这个功能. 然而, GVM 通过几条命令使得Go环境的更新变得很简。

在开发Jenkins X很长一段时间后，GVM使用起来将会特别的方便. Jenkins X之后的版本将会用最新版版的Go语言进行编译，因此如果想与社区开发同步的话，将会需要更新Go环境。

## 创建一个 GitHub 账号

如果你想要贡献代码的话，需要创建一个Github账号. 登录 [www.github.com/join](https://github.com/join)注册个人账号。

## 在你的系统上安装 Git
Jenkins X 开发过程当中需要在本机安装Git 客户端。Git 的使用学习不包含在 Jenkins X 的文档中， 如果你不确定从哪里开始的话，我们推荐通过[Git book][gitbook]学习使用Git的基本知识。 使用的词汇将会通过注解进行解释。

Git 是一个[版本控制系统](https://en.wikipedia.org/wiki/Version_control), 用于记录源代码的变化。 为了不重复造轮子，Jenkins X 使用了第三方的软件包来扩展功能。

Go 提供了`get`的命令来帮助下载软件包以配置工作环境。这些软件包的源码信息在Git中记录。 `get` 会与承载这些软件包的Git服务器端进行交互来下载所有的依赖。

回到终端中，输入`git version` 并按回车，检验是否安装Git。 如果返回的是一个版本号信息，那么可以跳过下面的配置。否则的话[下载](https://git-scm.com/downloads)最新版的Git并根据[安装文档](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)进行安装。

最后，再一次输入`git version`检验Git是否安装成功。

### Git 图形化前端

There are several [GUI clients](https://git-scm.com/downloads/guis) that help you to operate Git. Not all are available for all operating systems and maybe differ in their usage. Thus, so we will use the command line since the commands are everywhere the same.

### 在你的系统上安装 Hub（可选）

Hub is a great tool for working with GitHub. The main site for it is [hub.github.com](https://hub.github.com/). Feel free to install this little Git wrapper.

On a Mac, you can install [Hub](https://github.com/github/hub) using [Homebrew](https://brew.sh):

```
brew install hub
```

Now we'll create an [alias in Bash](http://tldp.org/LDP/abs/html/aliases.html) so that typing `git` actually runs `Hub`:

```
echo "alias git='hub'" >> ~/.bash_profile
```

Confirm the installation:

```
git version 2.6.3
hub version 2.2.2
```

## 设置你的工作备份

The working copy is set up locally on your computer. It's what you'll edit, compile, and end up pushing back to GitHub. The main steps are cloning the repository and creating your fork as a remote.

### 克隆仓库

We assume that you've set up your `GOPATH` (see the section above if you're unsure about this). You should now copy the Jenkins X repository down to your computer. You'll hear this called "clone the repo". GitHub's [help pages](https://help.github.com/articles/cloning-a-repository/) give us a short explanation:

> When you create a repository on GitHub, it exists as a remote repository. You can create a local clone of your repository on your computer and sync between the two locations.

We're going to clone the [master Jenkins X repository](https://github.com/jenkins-x/jx). That seems counter-intuitive, since you won't have commit rights on it. But it's required for the Go workflow. You'll work on a copy of the master and push your changes to your own repository on GitHub.

So, let's clone that master repository:

```
go get -v -u github.com/jenkins-x/jx
```

Jenkins X relies on [Testify](https://github.com/stretchr/testify) for testing Go code. If you don't already have it, get the Testify testing tools:

```
go get github.com/stretchr/testify
```

### 派生仓库

If you're not fimiliar with this term, GitHub's [help pages](https://help.github.com/articles/fork-a-repo/) provide again a simple explanation:

> A fork is a copy of a repository. Forking a repository allows you to freely experiment with changes without affecting the original project.

#### 手工派生

Open the [Jenkins X repository](https://github.com/jenkins-x/jx) on GitHub and click on the "Fork" button in the top right.

![Fork button](/images/contribute/development/forking-a-repository.png)

Now open your fork repository on GitHub and copy the remote url of your fork. You can choose between HTTPS and SSH as protocol that Git should use for the following operations. HTTPS works always [if you're not sure](https://help.github.com/articles/which-remote-url-should-i-use/).

![Copy remote url](/images/contribute/development/copy-remote-url.png)

Switch back to the terminal and move into the directory of the cloned master repository from the last step.

```
cd $GOPATH/src/github.com/jenkins-x/jx
```

Now Git needs to know that our fork exists by adding the copied remote url:

```
git remote add <YOUR-GITHUB-USERNAME> <COPIED REMOTE-URL>
```

#### 使用 Hub 派生

Alternatively, you can use the Git wrapper Hub. Hub makes forking a repository easy:

```
git fork
```

That command will log in to GitHub using your account, create a fork of the repository that you're currently working in, and add it as a remote to your working copy.

#### 验证

Let's check if everything went right by listing all known remotes:

```
git remote -v
```

The output should look similar:

```
digitalcraftsman    git@github.com:digitalcraftsman/hugo.git (fetch)
digitalcraftsman    git@github.com:digitalcraftsman/hugo.git (push)
origin  https://github.com/jenkins-x/jx (fetch)
origin  https://github.com/jenkins-x/jx (push)
```

## Jenkins X Git 贡献流程

### 创建新的分支

You should never develop against the "master" branch. The development team will not accept a pull request against that branch. Instead, create a descriptive named branch and work on it.

First, you should always pull the latest changes from the master repository:

```
git checkout master
git pull
```

Now we can create a new branch for your additions:

```
git checkout -b <BRANCH-NAME>
```

You can check on which branch your are with `git branch`. You should see a list of all local branches. The current branch is indicated with a little asterisk.

### 贡献文档

Perhaps you want to start contributing to the Jenkins X docs. If so, you can ignore most of the following steps and focus on the `/docs` directory within your newly cloned repository. You can change directories into the Jenkins X docs using `cd docs`.

You can start Jenkins X's built-in server via `hugo server`. Browse the documentation by entering [http://localhost:1313](http://localhost:1313) in the address bar of your browser. The server automatically updates the page whenever you change content.

We have developed a [separate Jenkins X documentation contribution guide][docscontrib] for more information on how the Jenkins X docs are built, organized, and improved by the generosity of people like you.

### 构建 Jenkins X

While making changes in the codebase it's a good idea to build the binary to test them:

```
go build -o hugo main.go
```

### 测试 
Sometimes changes on the codebase can cause unintended side effects. Or they don't work as expected. Most functions have their own test cases. You can find them in files ending with `_test.go`.

Make sure the commands `go test ./...` passes, and `go build` completes.

### 格式 
The Go code styleguide maybe is opiniated but it ensures that the codebase looks the same, regardless who wrote the code. Go comes with its own formatting tool. Let's apply the styleguide to our addtions:

```
go fmt ./...
```

Once you made your additions commit your changes. Make sure that you follow our [code contribution guidelines](https://github.com/jenkins-x/jx/blob/master/CONTRIBUTING.md):

```
# Add all changed files
git add --all
git commit --message "YOUR COMMIT MESSAGE"
```

The commit message should describe what the commit does (e.g. add feature XYZ), not how it is done.

### 修改提交

You noticed some commit messages don't fulfill the code contribution guidelines or you just forget something to add some files? No problem. Git provides the necessary tools to fix such problems. The next two methods cover all common cases.

If you are unsure what a command does leave the commit as it is. We can fix your commits later in the pull request.

#### 修改最后一次提交

Let's say you want to modify the last commit message. Run the following command and replace the current message:

```
git commit --amend -m"YOUR NEW COMMIT MESSAGE"
```

Take a look at the commit log to see the change:

```
git log
# Exit with q
```

After making the last commit you may forgot something. There is no need to create a new commit. Just add the latest changes and merge them into the intended commit:

```
git add --all
git commit --amend
```

#### 修改多次提交

{{% warning "Be Careful Modifying Multiple Commits"%}}
Modifications such as those described in this section can have serious unintended consequences. Skip this section if you're not sure!
{{% /warning %}}

This is a bit more advanced. Git allows you to [rebase](https://git-scm.com/docs/git-rebase) commits interactively. In other words: it allows you to rewrite the commit history.

```
git rebase --interactive @~6
```

The `6` at the end of the command represents the number of commits that should be modified. An editor should open and present a list of last six commit messages:

```
pick 80d02a1 tpl: Add hasPrefix to the template funcs' "smoke test"
pick aaee038 tpl: Sort the smoke tests
pick f0dbf2c tpl: Add the other test case for hasPrefix
pick 911c35b Add "How to contribute to Jenkins X" tutorial
pick 33c8973 Begin workflow
pick 3502f2e Refactoring and typo fixes
```

In the case above we should merge the last to commits in the commit of this tutorial (`Add "How to contribute to Jenkins X" tutorial`). You can "squash" commits, i.e. merge two or more commits into a single one.

All operations are written before the commit message. Replace "pick" with an operation. In this case `squash` or `s` for short:

```
pick 80d02a1 tpl: Add hasPrefix to the template funcs' "smoke test"
pick aaee038 tpl: Sort the smoke tests
pick f0dbf2c tpl: Add the other test case for hasPrefix
pick 911c35b Add "How to contribute to Jenkins X" tutorial
squash 33c8973 Begin workflow
squash 3502f2e Refactoring and typo fixes
```

We also want to rewrite the commits message of the third last commit. We forgot "docs:" as prefix according to the code contribution guidelines. The operation to rewrite a commit is called `reword` (or `r` as shortcut).

You should end up with a similar setup:

```
pick 80d02a1 tpl: Add hasPrefix to the template funcs' "smoke test"
pick aaee038 tpl: Sort the smoke tests
pick f0dbf2c tpl: Add the other test case for hasPrefix
reword 911c35b Add "How to contribute to Jenkins X" tutorial
squash 33c8973 Begin workflow
squash 3502f2e Refactoring and typo fixes
```

Close the editor. It should open again with a new tab. A text is instructing you to define a new commit message for the last two commits that should be merged (aka "squashed"). Save the file with <kbd>CTRL</kbd>+<kbd>S</kbd> and close the editor again.

A last time a new tab opens. Enter a new commit message and save again. Your terminal should contain a status message. Hopefully this one:

```
Successfully rebased and updated refs/heads/<BRANCHNAME>.
```

Check the commit log if everything looks as expected. Should an error occur you can abort this rebase with `git rebase --abort`.

### 推送提交

To push our commits to the fork on GitHub we need to speficy a destination. A destination is defined by the remote and a branch name. Earlier, the defined that the remote url of our fork is the same as our GitHub handle, in my case `digitalcraftsman`. The branch should have the same as our local one. This makes it easy to identify corresponding branches.

```
git push --set-upstream <YOUR-GITHUB-USERNAME> <BRANCHNAME>
```

Now Git knows the destination. Next time when you to push commits you just need to enter `git push`.

If you modified your commit history in the last step GitHub will reject your try to push. This is a safety-feature because the commit history isn't the same and new commits can't be appended as usual. You can enforce this push explicitly with `git push --force`.

## 打开一个 Pull Request

We made a lot of progress. Good work. In this step we finally open a pull request to submit our additions. Open the [Jenkins X master repository](https://github.com/jenkins-x/jx/) on GitHub in your browser.

You should find a green button labeld with "New pull request". But GitHub is clever and probably suggests you a pull request like in the beige box below:

![Open a pull request](/images/contribute/development/open-pull-request.png)

The new page summaries the most important information of your pull request. Scroll down and you find the additions of all your commits. Make sure everything looks as expected and click on "Create pull request".

### 同意贡献者授权协议

Last but not least you should accept the contributor license agreement (CLA). A new comment should be added automatically to your pull request. Click on the yellow badge, accept the agreement and authenticate yourself with your GitHub account. It just takes a few clicks and only needs to be done once.

![Accept the CLA](/images/contribute/development/accept-cla.png)

### 自动化构建

We use the [Travis CI loop](https://travis-ci.org/jenkins-x/jx) (Linux and OS&nbsp;X) and [AppVeyor](https://ci.appveyor.com/project/jenkins-x/jx/branch/master) (Windows) to compile Jenkins X with your additions. This should ensure that everything works as expected before merging your pull request. This in most cases only relevant if you made changes to the codebase of Jenkins X.

![Automic builds and their status](/images/contribute/development/ci-errors.png)

Above you can see that Travis wasn't able to compile the changes in this pull request. Click on "Details" and try to investigate why the build failed. But it doesn't have to be your fault. Mostly, the `master` branch that we used as foundation for your pull request should build without problems.

If you have questions, leave a comment in the pull request. We are willing to assist you.

## 从哪里开始？

Thank you for reading through this contribution guide. Hopefully, we will see you again soon on GitHub. There are plenty of [open issues][issues] for you to help with.

Feel free to [open an issue][newissue] if you think you found a bug or you have a new idea to improve Jenkins X. We are happy to hear from you.

## 学习 Git 和 Golang 的参考

* [Codecademy's Free "Learn Git" Course][codecademy] (免费)
* [Code School and GitHub's "Try Git" Tutorial][trygit] (免费)
* [The Git Book][gitbook] (免费)
* [Go Bootcamp][gobootcamp]
* [GitHub Pull Request Tutorial, Thinkful][thinkful]


[codecademy]: https://www.codecademy.com/learn/learn-git
[contributors]: https://github.com/jenkins-x/jx/graphs/contributors
[docscontrib]: /contribute/documentation/
[forums]: https://discourse.jenkins-x.io
[gitbook]: https://git-scm.com/
[gobootcamp]: http://www.golangbootcamp.com/book/get_setup
[godl]: https://golang.org/dl/
[goinstall]: https://golang.org/doc/install
[gvm]: https://github.com/moovweb/gvm
[issues]: https://github.com/jenkins-x/jx/issues
[newissue]: https://github.com/jenkins-x/jx/issues/new
[releases]: /getting-started/
[setupgopath]: https://golang.org/doc/code.html#Workspaces
[thinkful]: https://www.thinkful.com/learn/github-pull-request-tutorial/
[trygit]: https://try.github.io/levels/1/challenges/1
