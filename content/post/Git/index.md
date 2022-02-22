---
title: "Git"
date: 2022-02-15T00:01:02+08:00
draft: false
image: b1.jpg
tags:
    - 版本控制系统
    - Git
---

[TOC]

# Git

## 1.版本控制系统（VCS)

### 1.1 基本概念    `...`行内代码

- 版本控制系统（VCS）最基本的功能就是版本控制。而所谓版本控制，意思就是在文件的修改历史中保存修改历史，让i方便对文件的i修改工作。
- 我们常用的主流文本编辑器的undo功能其实就是版本控制。
- **但是VCS和文本编辑器的撤销功能相比，有一个很重要的区别就是：**
  - 对于程序代码而言：修改的生命周期很长，如果采用每次改动自动保存的形式来保存修改历史，将会导致改动历史非常频繁和无章可循。所以和文本编辑器的撤销功能不同，VCS保存修改历史，使用的是**主动提交改动**的机制。

## 2.分布式版本控制系统(DVCS)

### 2.1 工作模型

- 分布式（DVCS）与VCS的区别在于，分布式VCS除了中央仓库之外，还有本地仓库：团队的每一个成员的机器上都有一份本地昂库，这个仓库包含了所有版本历史，换句话说在这种工作模型内，你是和本地仓库交互。
- 工作流程
  - 提交代码到本地仓库
  - 在服务器上创建一个中央仓库，并把1中的提交推送到服务器的中央仓库。
  - 其他工作人员将中央仓库的所有内容克隆到本地，拥有了各自的本地仓库，此时进行并行开发
  - 在职过后的开发过程中，么个人都会独立负责开发一个功能，在这个功能的开发过程中，每个人都会把它的每一步改动提交到本地仓库（由于本地提交毋须立即推送到中央仓库，所以提交的内容不一定要是一个完整的功能额模块，而可以是某个步骤）
  - 当完成了某个功能的开发时，可以把与该功能相关的所有提交 **从本地仓库推送到中央仓库**
  - 而每次有人把新的提交推送到中央仓库是，另外的人就可以选择把这些提交同步到自己的机器上，并把他们和自己的本地代码合并

### 2.2优缺点分析

- 😵优点：①大多数操作可以在本地运行，速度更快。②由于可以提交到本地，因此可以分步提交代码，把代码提交做的更细，而不是一个提交包含很多代码，难以review也难以回溯。
- 🤠缺点：由于每一个旧机器都需要有完整的本地仓库，所以初次获取代码需要获取项目比较费时②本地占用的存储比中央式高

## 3.使用Git管理代码

### 3.1 前期准备

1. Github创建远程仓库 （.gitignore设置项目类型，是git仓库中的一个特俗的文本文件，记录了你不希望提交到仓库的目录和文件的名称或类型
2. 点击右边的Clone or downloda，然后把仓库的Clone地址复制到截切版
3. 在你喜欢的任意一个位置，打开Git Bash,**输入Git clone 刚复制的地址**（该过程可能会需要你去输入Github的用户名和密码）
4. 克隆完毕后，你的目录中会出现.git的隐藏目录，改了目录就是你的本地仓库，你的所有把那本信息都会存在这里。而.git所在的目录成为工作目录。
5. 一些git的基本指令在这里就不详细介绍了，可以查阅Git官方文档

## 4.Git基本工作模型

- 由于git在工作时，必须保证自己的本地仓库与中央仓库保持一致，因此，当你第一次从中央仓库拉去代码后，你的同事又push了一次，那么当你想要把自己的commits提交上去时，就需要先拉取同事的代码到本地。😧
- 当push时出现冲突：
  - 在现实的团队开发中，全队时同时并行开发的，所以必然会出现当一人push代码时，中央仓库已经被其他同事先一步push了的情况。
  - 那这种情况下，当我们像上面介绍的那样使用git pull指令拉取代码时，他并不会像之前那样直接结束，而是会出现一个输入提交信息的界面，这是因为pull操作返现不仅远程仓库有本地每天有的commits，本地也有远端仓库不具备的commits，它就会把远端和本地独有的commit合并，自动生成一个新的commit
  - 另一种情况，当出现冲突的部分是你和你的同事对某一个文件的某一处进行了不同的修改，Git就无法直接处理了，关于这点会在之后的文章中介绍🦄

## 5.Head、master、branch的使用讲解

👇👇👇👇👇👇👇👇👇👇👇👇

When Typed "git log"

![image-20220210161411687.png](https://s2.loli.net/2022/02/14/nUwiuFzc2vDJ13g.png)

- 第一行的commit后面的括号里的 *HEAD-> main*，是只想这个commit的引用。在Git操作中，经常会需要对指定Commit进行操作，而每一个Commit都会有它唯一的指定方式——它的SHA-1校验和，也就是每个commit中那串黄色的字符。由于SHA-1重复的概率极低，因此你可以使用它来指代某一个commit，也可以只是用他的前几位来指代。

- Head ：当前的Commit的引用：也就是说当前工作目录所对应的Commit。All in all,Commit在哪里，Head就在哪里。、

- Branch：是Git中的另一个引用，Head除了可以指向commit，还可以指branch；当他指向branch时，会通过这个branch来简介地指向某个commit；Besides:person_frowning:当Head在提交时自动向前移动时，它会带着它所指向地Branch一起移动。

  - 如上图 HEAD->main,main就是当前分支的名字。

  - ![image-20220210162748358.png](https://s2.loli.net/2022/02/14/9xeTpcd86Zky72Q.png)

  - ```
    当你输入Git commit,Head就会带着这个分支，移向下一个commit
    ```

    ![image-20220210162916120.png](https://s2.loli.net/2022/02/14/BWpratdZP3NOhJM.png)

  - 你可以通过git log指令 对这个逻辑进行验证，这里就不详细说明了

- Master：默认branch

  - 一般而言，master是Git的默认主分支，它具备一些特点：

    - 当你新建仓库时，是不存在任何commit的，但在它创建第一个commit时，会把master指向它，并把Head指向master :grin:

    - 当某人使用git clone时，除了从远程仓库把.git这个仓库目录下栽到工作目录中，还会checkout（签出，后面讲解） master

    - 类似于这个过程

      ![image-20220210164300791.png](https://s2.loli.net/2022/02/14/jAR91PtpOvlYQJa.png)

  - branch的通俗解释：

    - 表面上看，branch是一个指向commit的引用，但是你也可以把它理解为从初始  *commit* 到branch所指向的*commit*之间的所有Commits的一个串

    - branch相关指令

    - ```tex
      创建branch： git branch +分支名 
      切换branch： git checkout +分支名
      git checkout -b 名称是这步操作合并执行
      删除branch：
      git branch -d 分支名
      ```

    - branch 创建 切换 删除的流程

    - ![image-20220210165017931.png](https://s2.loli.net/2022/02/14/egZFWkEVPOl3JcG.png)

    - ![image-20220210165036392.png](https://s2.loli.net/2022/02/14/gA4ariTKd8BXVno.png)

    - 两个分支出现分叉 :pensive:

      ![image-20220210165124086.png](https://s2.loli.net/2022/02/14/wKVqdTLc5CNuQlk.png)

    - Notice :imp:Head指向的branch不可以删除；删除branch只是删除这个引用，并不会删除任何的*commit*；没有被合并到master过的branch在删除时会失败，担心删除错误，但是若想强制删除 把d大写即可

## 6. Push的本质

- 之前讲的：😳Push指令所做的事是把你的本地提交上传到中央仓库去，用本地的内容覆盖掉远端的内容，其实这个说法是不准确的🐷。

- **实质上**，Push所做的事是：把当前branch的位置上传到远端仓库，并把它的路径上的```commits```一并上传。🙏比如，当前我的本地仓库有一个分支叫```master```,它超前了远程仓库两个提交（如下图所示）；另外还有一个新建的`branch`叫`feature1`，远程仓库还没有记载过，具体情况如下图：🤥

  

![image-20220213151805782.png](https://s2.loli.net/2022/02/14/QpCk3SVBK1v8m2J.png)

- 此时当我执行`git push`,就会把`master`的最新位置更新到远端，并把它的路径上的`5,6`两个·commit`上传，示例如下：

![image-20220213151955662.png](https://s2.loli.net/2022/02/14/rPfCO2LuBZKqTiy.png)

- 可以注意到此时远程仓库与master分支的状态同步，但是与`feature1`的状态不同步，此时可以切换到`feature1`分支，然后同样的 操作就可以实现将`commit 4`提交到远程。🦉

- 具体的操作步骤如下：

  ```bash
  git checkout feature1
  git push origin feature1
  ```

- 可以注意到，这里的git push比之前多了两个参数：`origin``feature1`,其中`orgin`是远程仓库的别名，是你在`git clone`时Git默认的名称，`feature1`则是远程仓库中目标`branch`的名称。

- Notice：在Git 2.0版本，`git push`只能上传从远端`clone`或`git pull`下来的分支,而你本地自己创建的分支，在提交时需要手动指定目标仓库和目标分支。

- 🧐好吧，其实你也可以通过`git config`指令来设置`push.deafault`的值来改变`push`的行为逻辑；如果有兴趣，可以点击阅读  [Git-Config](https://git-scm.com/docs/git-config#git-config-pushdefault)

- Okay，当你将Feature1推送到远程仓库时，你会发现，远程仓库的`Head`并没有和本地一样指向`feature1`，这是因为`Push`并不会上传本地的`HeAD`的指向，而是仅仅只上传当前`branc`·的指向。⚙实际上，远程仓库的`Head`永远指向它的默认分支（即master），并随着该默认分支的移动而移动。👳👳👳

## 7.merge：合并commits

- Actually，`pull`的内部操作其实时先`fetch`即把远程仓库拉取到本地，在使用`merge`来把远端仓库的新的`commits`合并到本地，接下来会详细解释下什么是merge。

- 先来段官方解释：**从目标`commit`和当前`commit`（Head所指向的commit）分叉的位置起，把目标`commit`的路径上所有`commit`的内容一并应用到当前`commit`,然后自动生成一个新的`commit`**.

- emm 🙃如图所示

  ![image-20220213155935084.png](https://s2.loli.net/2022/02/14/AZQTHiEBcsk4KDF.png)

  当你此时执行`git merge branch1`,Git会把`5`和`6`这两个`commit`一起和`4`合并，并生成一个新的提交，并体哦啊转到信息提交填写界面：

  ![image-20220213160121658.png](https://s2.loli.net/2022/02/14/pbZarv37kgd6HLN.png)

  在信息提交完成之后，merge过程就结束了。

  ![image-20220213160153696.png](https://s2.loli.net/2022/02/14/kpARVdKT9zsonGv.png)

  ------

- **Merge的特殊情况处理**

  1. **Conflict Happend** 😭

     *常规的`merge`在执行合并操作的时候，具备一定的自动和合并能力：比如当一个分支对A文件进行了修改，另一个分支对B文件进行了修改，那么合并的结果就是A和B都被修改；如果两个分支都修改了同一个文件的不同位置，那么合并后就是两处修改都保存*，那么问题来了，当你修改的是同一个文件的同一位置，merge操作会怎么处理呢，这种情况我们成为 **Conflict**

     假设我们呢正处于冲突的情况：

     ```bash
     git merge feature1
     ```

     ![image-20220213161618900.png](https://s2.loli.net/2022/02/14/rDa1HCGIdlmtFV6.png)

     提示信息会说你需要把冲突解决后提交

     - 解决冲突

       当你打开重现冲突的文件时，你会发现虽然Git没有帮你自动完成`Merge`但是它对文件还是做了一些工作的，也就是将分支冲突的内容放在了一起，并且标出了它们的出处，Like this：

       ![image-20220213161954654.png](https://s2.loli.net/2022/02/14/Gp6UkAPo8aSDXef.png)

       你只需要删除掉你不想保留的分支的修改，并删除Git添加的那三行`<<<` `===``>>>`的辅助文字，save and quit，就解决了冲突👬。当然也有一些辅助公寓用于解决冲突，可以自行搜索。

     - 解决完冲突就可以手动提交了：`git add 出现冲突的文件名`  然后 `git commit`。

       ![image-20220213162423167.png](https://s2.loli.net/2022/02/14/mTH7PhQpSGJzncu.png)

       😱可以看到，被冲突阻断的`merge`，在手动`commit`时依然会自动填写提交信息，这是因为在冲突发生后，Git仓库处于一个【冲突待解决】的状态，在这种状态下，Git会自动添加提交信息。

     - 当然如果你不知道这个冲突该怎么解决，所以你决定放弃处理，也需要执行一次`merge --avort`来手动取消它，这样你的Git仓库会回到`merge`之前的状态。

  2. **HEAD领先于目标commit**😓

     ![image-20220213163035450.png](https://s2.loli.net/2022/02/14/FGu7fBh2etPcVNK.png)

  3. 这时`merge`并不会创建一个新的`commit`来进行合并操作，在这种情况下，Git什么野不会处理，是一个**空操作**。

  4. **Head落后于目标commit**👊

     这种操作会让Head直接移动到，目标分支所指向的commit，这种操作有一个专有操作，叫做"fast-forward"  。

     

## 8.Feature Branching

- Feature Branching时目前最流行的gGit工作流，它的核心内容可以归纳为以下两点：
  1. 任何新的功能或bug修复全都新建一个branch来写
  2. brnach写完后，合并到master（默认的主分支），然后删掉这个branch

##  9.About 'add'

1. **add的基本用法**

   `git add fileName`

   还可以使用`git add .`来把当前工作目录下的所有改动全部放进暂存区

   **其实，add添加的时文件改动，而不是文件名**

- **查看git保存的历史记录**🆑

  ```bash
  git log     --查看历史记录
  git log -p  --可以看到每一个commit的每一行改动
  git log -stat --查看简要统计，适用于大概看以下改动内容
  git show +某一个commit的引用 --看任意一个commit
  git diff -staged --显示你即将提交的内容，换句话说，就是如果你立即输入`git commit`，你将提交什么  该指令中staged==cached
  
  
  ```

  

## 10. 用rebase代替merge的工作

- `Rebase`：把你指定的`commit`以及它所在的commit串，以指定的目标commit为基础，一次重新提交一遍，具体过程如下图：

  ![image-20220214005051305.png](https://s2.loli.net/2022/02/14/DXQRpT6lcz8kSB3.png)

  ![image-20220214005112522](C:\Users\请输入姓名\AppData\Roaming\Typora\typora-user-images\image-20220214005112522.png)

  可以看出，通过 `rebase`，`5` 和 `6` 两条 `commit`s 把基础点从 `2` 换成了 `4` ；另外，在 `rebase` 之后，记得切回 `master` 再 `merge` 一下，把 `master` 移到最新的 `commit`

- Notice🎃：为了避免和远端仓库发生冲突，一般不要从 `master` 向其他 `branch` 执行 `rebase` 操作。🎃

------



## 11.Git应用实例

### 11.1 刚提交的代码，写错了怎么办？🙃

- 一种解决方法是再新写一个关于修改这个错误的`commit`

- 另一种则是通过：`commit -—amend`；在提交时，如果加上 `--amend` 参数，Git 不会在当前 `commit` 上增加 `commit`，而是会把当前 `commit` 里的内容和暂存区（stageing area）里的内容合并起来后创建一个新的 `commit`，**用这个新的 `commit` 把当前 `commit` 替换掉**🎉

  ```bash
  git add  修改后的文件
  git commit --amend
  ```

### 11.2错误的不是最新的提交，而是倒数第二个怎么办😥

- 这时候就可以通过 `rebase -i` 来找指定当前分支要`rebase`的`commit`链中的每一个`commit`是否需要修改。因此你可以做如下操作

  ```bash
  git log
  ```

  ![image-20220214112509390.png](https://s2.loli.net/2022/02/14/9m2sa18gVrEiSRp.png)

  ```bash
  git rebase -i HEAD^^
  ```

  ```htm
  说明：在 Git 中，有两个「偏移符号」： ^ 和 ~。
  
      ^ 的用法：在 commit 的后面加一个或多个 ^ 号，可以把 commit 往回偏移，偏移的数量是 ^ 的数量。例如：master^ 表示 master 指向的 commit 之前的那个 commit； HEAD^^ 表示 HEAD 所指向的 commit 往前数两个 commit。
  
      ~ 的用法：在 commit 的后面加上 ~ 号和一个数，可以把 commit 往回偏移，偏移的数量是 ~ 号后面的数。例如：HEAD~5 表示 HEAD 指向的 commit往前数 5 个 commit。
  
  ```

  执行后，会出现如下界面：

  ![image-20220214112858509.png](https://s2.loli.net/2022/02/14/upId4GCtUqvwEgF.png)

  - 这个编辑界面的最顶部，列出了将要「被 rebase」的所有 `commit`s，也就是倒数第二个 `commit` 「增加常见笑声集合」和最新的 `commit`「增加常见哭声集合」。需要注意，这个排列是正序的，旧的 `commit` 会排在上面，新的排在下面。

  - 你的目标是修改倒数第二个 `commit`，也就是上面的那个「增加常见笑声集合」，所以你需要把它的操作指令从 `pick` 改成 `edit` 。 `edit` 的意思是「应用这个 commit，然后停下来等待继续修正」

    ![image-20220214113013686.png](https://s2.loli.net/2022/02/14/ZP8DsItAmHiY43G.png)

    把 `pick` 修改成 `edit` 后，就可以退出编辑界面了：此时`rebase` 过程已经停在了第二个 `commit` 的位置，那么现在你就可以去修改你想修改的内容了。

  - `git add 修改后的文件`   `git commit --amend`

  - 修复完成后 使用 `git rebase --continue`来继续`rebase`过程，把后面的commit 直接应用上去

  - 可能看文字描述有点不能理解，👌上图：

    ```git rebase -i HEAD^^```

    ![image-20220214115039540.png](https://s2.loli.net/2022/02/14/JjXgWf4xl5d8IkZ.png)

    ![image-20220214115051199](C:\Users\请输入姓名\AppData\Roaming\Typora\typora-user-images\image-20220214115051199.png)

    `git rebase --continue`

    ![image-20220214115130997.png](https://s2.loli.net/2022/02/14/E9ZPy4aSn2ilWsR.png)

### 11.3 甚至都不想修改，删除算了

- 丢弃最新的提交使用：`git reset --hard HEAD^` 

  ```
  HEAD^` 表示你要恢复到哪个 `commit`。因为你要撤销最新的一个 `commit`，所以你需要恢复到它的父 `commit` ，也就是 `HEAD^
  ```

- **Reset的实质：其实是重置HEAD以及它所指向的`branch`的位置的**，

  - 这样就需要提下`git reset --hard`和`git reset --soft`的区别了：前者是在重置 `HEAD` 和 `branch` 的同时，重置工作目录里的内容；后者则是会在重置 `HEAD` 和 `branch` 时，保留工作目录和暂存区中的内容，并把重置 `HEAD` 所带来的新的差异放进暂存区。
  - 例如  hard：你新修改了一个文件，但是并没有提交，使用了hard指令，此时你的工作目录里的新改动也消失了，并与reset切换到的commit保持同样的内容；soft则是会保留工作目录和暂存区的内容。

- 丢弃的不是最新的提交  ：

  那就变基到你想删除的提交之前的那个COMMIT：`git rebase -i HEAD~?`，然后再提示信息界面删除那个提交即可。

- 关于`rebase -i`进行说明

  **rebase -i其实可以理解为把当前head所指向的commit之前的某个提交作为基点，从该基点开始，对后面的当前分支的commit进行提交，但是它会先给个提示信息界面用于你去选择对基点之后的commit进行操作**

- **方便的撤销：git onto** 🤘

  ```bash
  git rebase --onto 目标commit 起点commit 终点commitv  Notice:起点不包含再rebase中
  ```

### 11.4 代码已经push上去了🔐，并合并到master

- 在这种情况下，你就需要做一个把这行代码还原回来的提交：`revert`

  `git revert HEAD^`这行代码会增加一条新的提交，它的内容和倒数第二个`commit`是相反的，从而达到撤销的目的。⚠
  
  

## 12.CheckOut本质

- 前面说到`checkout`可以用来切换 `branch`；但是实质上，它的功能其实是：**签出指定的`commit`**🤪，通俗的讲，就是把 `HEAD` 指向指定的 `branch`，然后签出这个 `branch` 所对应的 `commit` 的工作目录。所以同样的，`checkout` 的目标也可以不是 `branch`，而直接指定某个 `commit`✏
- checkout和reset都可以切换head的位置，但是reset同时移动HEAD和它所指向的branch，而checkout只移动head

## 13. 关于.gitignore

- 在 Git 中有一个特殊的文本文件：`.gitignore`。这个文本文件记录了所有你希望被 Git 忽略的目录和文件。具体的规则可查阅 👉  [💾](https://link.juejin.cn/?target=https%3A%2F%2Fgit-scm.com%2Fdocs%2Fgitignore)  👈

## 14.使用git常出现的错误(REAMINS TO BE DONE)

