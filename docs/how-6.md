# "十万个"怎么做：如何玩转 `Gitflow`

## 是什么

在介绍 `Gitflow` 之前，我先介绍下 `git` 的诞生背景。long long ago（1991年），
有一个叫 Linus 的芬兰人开源了大名鼎鼎的 `Linux` 系统，既然是开源的，
所以呢就有很多人给 `Linux` 提代码，然后 Linus 在手动合并，
当时提交代码是通过 [diff](https://www.ruanyifeng.com/blog/2012/08/how_to_read_diff.html)
的方式啊，这个东西我试了下，如果文件数量少并且文件内容少还行，如果多了估计"王德发"了。

事实也是如此，随着 `Linux` 项目越来越大，社区的人也越来越多，社区里也对这种方式表达了强烈的不满。
于是 Linux 用了一个"免费版"的商业版本控制系统 `BitKepper` (现在开源了)。由于本质上它是一个付费的软件，
那 `Linux` 社区里这帮富有开源精神的人看不过去了，就破解人家的协议。那人家肯定生气啊，说了只要 Linus 公开"道歉"下就行。
然后呢，这个 Linus 也不是善茬，人家用了两周自己写了一个版本控制系统：`git`。（时间发生在2005年）

从那以后，`git` 发展迅速，很多个人或组织都在用，很快就成为了最流行的版本控制系统。由于团队规模和业务都不同，出现了很多种模型（使用 `git `的方式）,
比如： `Gitflow` 、`GitHub flow`、`GitLab flow`等。

总的来说，`Gitflow` 就是一种使用 `git` 的方式，规定如何命名分支、合并分支、打tag等操作。就像现在你的公司规定的 `git` 规则一样，
只不过 `Gitflow` 更出名一些。

那我们以上面这个背景下，想象一下 `Gitflow` 的作者（Vincent Driessen）是怎么想的呢？


`Gitflow` 图例：

<img width="500" src="https://nvie.com/img/git-model@2x.png"/>

[图片来源](https://nvie.com/posts/a-successful-git-branching-model/)

## 为什么

* 对团队来讲，有利于多人协作，保证每个人在项目迭代的周期内做正确的事儿。对新人来讲，也能很快的熟悉工作流（相信大部分的程序员或多或少的听过或者用过）。
* 对项目来说，有利于查看项目发布以来的历史，查看各个阶段做了哪些功能迭代和 bug 修复。
* 对维护和或者开发者来说，有利于查看每个人某个特定时段做了哪些事，比如在 finish release 阶段时，可以 review 代码。也可以针对某个线上版本快速回滚。

## 怎么做

在我们开始时间 `Gitflow` 之前，我们先了解下该模型下的分支类型。

主要包括：

### 主分支

* master
* develop

master 分支是一个原始的主分支。develop 分支是与 master 分支并行的分支。develop 最开始是从 master 分出来的。

master 一般就是线上（也可以说生产）环境对应的分支。所以每次要上线一个版本时，就要把**包含最终代码的分支**（注意这里，这里意味着该分支上已经没有影响线上的 bug 了，并且相关配置：版本号、BaseUrl 都是正确的了）合并到 master 分支。
**一般只允许 hotfix 和 release 分支合并到 master**。

develop 是和 master 并行的分支，但是一般是优先于 master 的。基本上其他分支都是从 develop checkout（以下用"检出"代替） 来的。

### 支持分支

* Feature branches
* Release branches
* Hotfix branches

这些分支都有特殊的含义。接下来会详细说明：

#### Feature branches

1、一般会从 develop 检出；

2、结束后必须合并到 develop；

3、分支命名不能和主分支和其他支持分支冲突；

该分支一般是在开始一个新的需求时，从 develop 检出相应的分支。当开发结束后合并到 develop 分支，并作为之后的 release 的上流分支。

#### Release branches

1、需要从 develop 检出；

2、结束后必须合并到 develop 和 master；

3、命名规则一般：release-*；

该分支是在 Feature branches 分支结束后，准备上线前的测试阶段时，从 develop 检出分支。
并在该分支进行 bug 修复和修改相关配置（版本号、环境变量等）。注意的是，禁止在该分支添加大功能，
因为这属于新特性，按理要放到下一个特性分支中进行。

> release 分支的主要流程就是：开发 -> 测试 -> 修改bug -> 重新部署到测试环境 -> 再次测试。
> 这个流程一直重复，直到测试没问题，并且产品和运营等验收通。

#### Hotfix branches

1、一般是从 master 检出；

2、结束后必须合并到 develop 和 master；

3、命名规则一般：hotfix-*；

该分支一般是在线上出问题时用到，且分支基本上是必须从 master 检出的。原因是 develop 分支可能包含了新版本的代码，如果从 develop 检出的话，
最后导致线上分支 master 包含不该出现的功能或 bug。

> 这里需要注意下，只有线上紧急的 bug 才会从 master 检出，否则会放到下次版本迭代中（放到 feature 中）。

### 实操环节

这里我们举两个比较常见的场景：

#### 同时只有一个需求进行

背景：

1、该项目是一个商城；

2、目前线上版本为 1.0.0；

3、现在新增一个收藏商品的功能；

##### 第一步：检出 feature branch，命名为：feat-goodsCollect


#### 同时有多个需求进行

## 总结

参考文章：
* https://blog.csdn.net/weixin_43117449/article/details/84573917
* https://nvie.com/posts/a-successful-git-branching-model/
* https://datasift.github.io/gitflow/IntroducingGitFlow.html
