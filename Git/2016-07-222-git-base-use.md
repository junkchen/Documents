# **Git自学之路（二）- Git初始配置和基本使用** #

在学习完本文之后，你应该能够配置并初始化一个仓库（repository）、开始或停止跟踪（track）文件、暂存（stage）或提交（commit)更改。 本章也将向你演示如何配置 Git 来忽略指定的文件和文件模式、如何迅速而简单地撤销错误操作、如何浏览你的项目的历史版本以及不同提交（commits）间的差异、如何向你的远程仓库推送（push）以及如何从你的远程仓库拉取（pull）文件。  

## **初次运行Git前的配置** ##

### **用户信息** ###

当安装完 Git 应该做的第一件事就是设置你的用户名称与邮件地址。 这样做很重要，因为每一个 Git 的提交都会使用这些信息，并且它会写入到你的每一次提交中，不可更改：

```git
$ git config --global user.name "Junk Chen"
$ git config --global user.email junkchen@vip.qq.com
```

“Junk Chen”是你自己设置的名字，junkchen@vip.qq.com是你的邮箱地址。  

再次强调，如果使用了  --global  选项，那么该命令只需要运行一次，因为之后无论你在该系统上做任何事情，Git 都会使用那些信息。当你想针对特定项目使用不同的用户名称与邮件地址时，可以在那个项目目录下运行没有 --global 选项的命令来配置。  

很多 GUI 工具都会在第一次运行时帮助你配置这些信息。  

### **检查配置信息** ###

如果想要检查你的配置，可以使用 git config --list 命令来列出所有 Git 当时能找到的配置。  

```git
$ git config --list
core.autocrlf=true
color.diff=auto
color.status=auto
color.branch=auto
help.format=html
user.name=JunkChen
user.email=junkchen@vip.qq.com
...
```

你可以通过输入 git config <key>： 来检查 Git 的某一项配置:  

```git
$ git config user.name
Junk Chen
```

## **获取帮助** ##

若你使用Git时需要获取帮助，有三种方法可以找到Git命令的使用手册：  

```git
$ git help <verb>
$ git <verb> --help
$ man git-<verb>
```

如想要获得config命令的手册，可执行：  

```git
$ git help config
```

这些命令很棒，因为你随时随地可以使用而无需联网。

安装和初始配置都已经设置好了，下面就来看看具体的应用了。  

## **获取Git仓库** ##

两种获取Git项目仓库的方法： 一是在现有项目或目录下导入所有文件到Git中； 二是从一个服务器克隆一个现有的Git仓库。  

### **在现有目录中初始化仓库** ###

进入该项目目录并输入：  

```git
$ git init
```

该命令将创建一个名为 .git 的子目录，这个子目录含有你初始化的  Git 仓库中所有的必须文件，这些文件是 Git 仓库的骨干。

### **克隆现有的仓库** ###

如果你想获得一份已经存在了的Git仓库的拷贝，可以使用git clone [url]。  

```git 
$ git clone https://github.com/libgit2/libgit2
```  

该命令执行后会在当前目录下创建一个名为 “libgit2” 的目录，并在在这个目录下初始化一个.git文件夹，从远程仓库拉取所有的数据放在改文件夹，然后从中读取最新版本的文件的拷贝。 如果你进入到这个新建的 libgit2 文件夹，你会发现所有的项目文件已经在里面了，准备就绪等待后续的开发和使用。 请将 https://github.com/libgit2/libgit2 替换为你自己的Git仓库地址。  

```git
$ git clone https://github.com/libgit2/libgit2 mylibgit
```

这将执行与上一个命令相同的操作，不过在本地创建的仓库名字变为 mylibgit。  

Git  支持多种数据传输协议。  上面的例子使用的是https://  协议，不过你也可以使用  git://  协议或者使用SSH  传输协议，比如  user@server:path/to/repo.git  。

## **记录每次更新到仓库** ##

现在我们有了一个真实项目的 Git 仓库，并从这个仓库中取出了所有文件的工作拷贝。 当我们对文件做了些修改，在完成一个阶段的目标后，就提交本次更新到仓库。  

请记住，你工作目录下的每一个文件都不外乎这两种状态：已跟踪或未跟踪。  已跟踪的文件是指那些被纳入了版本控制的文件，在上一次快照中有它们的记录，在工作一段时间后，它们的状态可能处于未修改，已修改或已放入暂存区。  工作目录中除已跟踪文件以外的所有其它文件都属于未跟踪文件，它们既不存在于上次快照的记录中，也没有放入暂存区。  初次克隆某个仓库的时候，工作目录中的所有文件都属于已跟踪文件，并处于未修改状态。  

编辑过某些文件之后，由于自上次提交后你对它们做了修改，Git  将它们标记为已修改文件。  我们逐步将这些修改过的文件放入暂存区，然后提交所有暂存了的修改，如此反复。    

### **检查当前文件的状态** ###

查看文件处于什么状态：  

```git
$ git status
```

### **跟踪新文件** ###

如果我们的工作目录中创建了新的文件，可使用git add开始跟踪一个文件。如跟踪 READEME.txt 文件：  

```git
$ git add READEME.txt
```

此时再运行 git status 命令，会看到 README 文件状态已发生改变。  

git add 命令使用文件或目录的路径作为参数；如果参数是目录的路径，该命令将递归地跟踪该目录下的所有文件。  

### **暂存已修改文件** ###

当一个已被跟踪的文件每次被修改后，需要使用 git add 把最新版本添加到暂存区方可进行提交更新，否则不会提交本次更改到仓库中。如 READEME.txt 文件被修改后添加暂存： 

```git
$ git add READEME.txt
```

git add 是个多功能命令：可以用它开始跟踪新文件，或者把已跟踪的文件放到暂存区，还能用于合并时把有冲突的文件标记为已解决状态等。可理解为 “添加内容到下一次提交中”。  

### **查看已暂存和未暂存的修改** ###

如果 git status 命令的输出对于你来说过于模糊，你想知道具体修改了什么地方，可以用 git diff 命令。  

```git
$ git diff
```

此命令比较的是工作目录中当前文件和暂存区域快照之间的差异， 也就是修改之后还没有暂存起来的变化内容。

若要查看已暂存的将要添加到下次提交里的内容，可以用 git diff --cached 命令。（Git 1.6.1 及更高版本还允许使用 git diff --staged，效果是相同的，但更好记些。）  

git  diff  本身只显示尚未暂存的改动，而不是自上次提交以来所做的所有改动。  所以有时候你一下子暂
存了所有更新过的文件后，运行 git diff 后却什么也没有，就是这个原因。  


### **提交更新** ###

当使用 git add 添加到暂存区后，就可以提交到仓库了。在此之前，请一定要确认还有什么修改过的或新建的文件还没有 git add 过，否则提交的时候不会记录这些还没有暂存起来的变化。这些修改过的文件只保留在本地磁盘。所以，每次准备提交前，先用 git status 看下，是不是都已暂存起来了，然后在运行提交命令 git commit ：  

```git 
$ git commit -m "Input your commit message"
```

使用 -m 选项，添加提交信息。  

到此，使用 Git 就完成了，初始化仓库、添加文件、提交更新到仓库的全过程。  

### **跳过使用暂存区** ###

尽管使用暂存区域的方式可以精心准备要提交的细节，但有时候这么做略显得繁琐。 Git 提供了一个跳过使用暂存区域的方式，只要在提交的时候，给 git commit 加上 -a 选项，Git就会自动把所有已经跟踪过的文件暂存起来一并提交，从而跳过 git add 步骤。  

```git
$ git commit -a -m "Commit message"
```

### **移除文件** ###

要从 Git 中移除某个文件，就必须要从已跟踪文件清单中移除（确切地说，是从暂存区域移除），然后提交。 可以用 git rm 命令完成此项工作，并连带从工作目录中删除指定的文件，这样以后就不会出现在未跟踪文件清单中了。  

```git
$ git rm <finame>
```

下一次提交时，该文件就不再纳入版本管理了。  如果删除之前修改过并且已经放到暂存区域的话，则必须要用强制删除选项  -f（译注：即  force  的首字母）。  这是一种安全特性，用于防止误删还没有添加到快照的数据，这样的数据不能被 Git 恢复。  

另外一种情况是，我们想把文件从  Git  仓库中删除（亦即从暂存区域移除），但仍然希望保留在当前工作目录中。 换句话说，你想让文件保留在磁盘，但是并不想让 Git 继续跟踪。 当你忘记添加 .gitignore 文件，不小心把一个很大的日志文件或一堆  .a  这样的编译生成文件添加到暂存区时，这一做法尤其有用。  为达到这一目的，使用 --cached 选项：  

```git
$ git rm --cached README
```

git rm 命令后面可以列出文件或者目录的名字，也可以使用 glob 模式。 比方说：  

```bash
$ git rm log/\*.log
```

注意到星号 * 之前的反斜杠 \， 因为 Git 有它自己的文件模式扩展匹配方式，所以我们不用 shell 来帮忙展开。此命令删除 log/ 目录下扩展名为 .log 的所有文件。 类似的比如：  

```bash
$ git rm \*~
```

该命令为删除以 ~ 结尾的所有文件。  

### **移动文件** ###

不像其它的 VCS 系统，Git 并不显式跟踪文件移动操作。 如果在 Git 中重命名了某个文件，仓库中存储的元数据并不会体现出这是一次改名操作。 不过 Git 非常聪明，它会推断出究竟发生了什么。  

既然如此，当你看到 Git 的 mv 命令时一定会困惑不已。 要在 Git 中对文件改名，可以这么做：

```git
$ git mv file_from file_to
```

它会恰如预期般正常工作。 实际上，即便此时查看状态信息，也会明白无误地看到关于重命名操作的说明：  

```git
$ git mv README.md README
$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)
    renamed:    README.md -> README
```

其实，运行 git mv 就相当于运行了下面三条命令：  

```git
$ mv README.md README
$ git rm README.md
$ git add README
```

如此分开操作，Git  也会意识到这是一次改名，所以不管何种方式结果都一样。  两者唯一的区别是，mv  是一条命令而另一种方式需要三条命令，直接用  git  mv  轻便得多。不过有时候用其他工具批处理改名的话，要记得在提交前删除老的文件名，再添加新的文件名。  

### **忽略文件** ###

一般我们总会有些文件无需纳入 Git 的管理，也不希望它们总出现在未跟踪文件列表。 通常都是些自动生成的文
件，比如日志文件，或者编译过程中创建的临时文件等。  在这种情况下，我们可以创建一个名为  .gitignore
的文件，列出要忽略的文件模式。 来看一个实际的例子：  

```git
$ cat .gitignore
*.[oa]
*~
```

第一行告诉 Git 忽略所有以 .o 或 .a 结尾的文件。第二行告诉  Git  忽略所有以波浪符（~）结尾的文件，许多文本编辑软件（比如  Emacs）都用这样的文件名保存副本。此外，你可能还需要忽略  log，tmp  或者  pid  目录，以及自动生成的文档等等。  要养成一开始就设置好.gitignore 文件的习惯，以免将来误提交这类无用的文件。    

文件 .gitignore 的格式规范如下：  

- 所有空行或者以 ＃ 开头的行都会被 Git 忽略。
- 可以使用标准的 glob 模式匹配。
- 匹配模式可以以（/）开头防止递归。
- 匹配模式可以以（/）结尾指定目录。
- 要忽略指定模式以外的文件或目录，可以在模式前加上惊叹号（!）取反。


所谓的  glob  模式是指  shell  所使用的简化了的正则表达式。  


### **查看提交历史** ###

在提交了若干更新后，想回顾下提交历史，可使用 *git log* 命令。  

```git
$ git log
```

## **撤销操作** ##

在任何一个阶段，你都有可能想要撤消某些操作。  这里，我们将会学习几个撤消你所做修改的基本工具。  注意，有些撤消操作是不可逆的。 这是在使用 Git 的过程中，会因为操作失误而导致之前的工作丢失的少有的几个地方之一。  

有时候我们提交完了才发现漏掉了几个文件没有添加，或者提交信息写错了。 此时，可以运行带有 --amend 选项的提交命令尝试重新提交：  

```git
$ git commit --amend
```

这个命令会将暂存区中的文件提交。  如果自上次提交以来你还未做任何修改（例如，在上次提交后马上执行了此命令），那么快照会保持不变，而你所修改的只是提交信息。  

文本编辑器启动后，可以看到之前的提交信息。 编辑后保存会覆盖原来的提交信息。  

例如，你提交后发现忘记了暂存某些需要的修改，可以像下面这样操作：  

```git
$ git commit -m 'initial commit'
$ git add forgotten_file
$ git commit --amend
```

最终你只会有一个提交 - 第二次提交将代替第一次提交的结果。  

### **取消暂存文件** ###

如何操作暂存区域与工作目录中已修改的文件， 这些命令在修改文件状态的同时，也会提示如何撤消操作。 例如，你已经修改了两个文件并且想要将它们作为两次独立的修改提交，但是却意外地输入了 git add * 暂存了它们两个。 如何只取消暂存两个中的一个呢？ git status 命令提示了你：  

```git
$ git add *
$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)
    renamed:    README.md -> README
    modified:   CONTRIBUTING.md
```

在  “Changes  to  be  committed”  文字正下方，提示使用  git  reset  HEAD  <file>...  来取消暂存。 所以，我们可以这样来取消暂存 CONTRIBUTING.md 文件：  

```git
$ git reset HEAD CONTRIBUTING.md
Unstaged changes after reset:
MCONTRIBUTING.md
$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

    renamed:    README.md -> README

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working
directory)

    modified:   CONTRIBUTING.md
```

### **撤销对文件的修改** ###

如果你并不想保留对 CONTRIBUTING.md 文件的修改怎么办？ 你该如何方便地撤消修改 - 将它还原成上次提交时的样子（或者刚克隆完的样子，或者刚把它放入工作目录时的样子）？  幸运的是，git  status  也告诉了你应该如何做。 在最后一个例子中，未暂存区域是这样：  

```git
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working
directory)
    modified:   CONTRIBUTING.md
```

它非常清楚地告诉了你如何撤消之前所做的修改。 让我们来按照提示执行：  

```git
$ git checkout -- CONTRIBUTING.md
$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)
    
  renamed:    README.md -> README
```

可以看到那些修改已经被撤消了。

记住，在  Git  中任何  已提交的  东西几乎总是可以恢复的。  甚至那些被删除的分支中的提交或使用  --amend  选项覆盖的提交也可以恢复。  然而，任何你未提交的东西丢失后很可能再也找不到了。

## **远程仓库的使用** ##

为了能在任意 Git 项目上协作，你需要知道如何管理自己的远程仓库。远程仓库是指托管在因特网或其他网络中你的项目的版本库。 与他人协作涉及管理远程仓库以及根据需要推送或拉取数据。  

### **查看远程仓库** ###

如果想查看你已经配置的远程仓库服务器，可以运行 *git remote* 命令。 它会列出你指定的每一个远程服务器的简写。 如果你已经克隆了自己的仓库，那么至少应该能看到 origin ，这是 Git 给你克隆的仓库服务器的默认名字：  

```git 
$ git remote
origin
```

可指定选项 *-v*，回显示需要读写远程仓库使用的 Git 保存的与其对应的URL。  

```git
$ git remote -v
origin  https://github.com/junkchen/Documents.git (fetch)
origin  https://github.com/junkchen/Documents.git (push)
```

若还想要查看某个远程仓库的更多信息，可以使用 *git remote show [remote-name]* 命令，如查看 origin ： 

```git
$ git remote show origin
```

### **添加远程仓库** ###

运行 *git remote add < shortname > < url >* 添加一个新的远程Git 仓库，同时指定一个你可以轻松引用的简写：  

```git
$ git remote add pb https://github.com/paulboone/ticgit
```

现在可以在命令行中使用字符串 **pb** 来代替整个 URL。如果你想获取 Paul 的仓库中有但你没有的信息，可以运行 *git fetch pb*： 

```git
$ git fetch pb
```

### **从远程仓库中抓取与拉取** ###

如上面所示，从远程仓库中获取数据，可以执行：  

```git 
$ git fetch <remote-name>
```

这个命令会访问远程仓库，从中拉取所有你还没有的数据。执行完成后，你将会拥有远程仓库中所有分支的引用，可随时合并或查看。  

若你使用 *git clone* 命令克隆一个仓库，命令会自动将其添加为远程仓库并默认以 “origin” 为简写。 所以，git fetch origin 会抓取克隆（或上一次抓取）后新推送的所有工作。 必须注意 git fetch 命令会将数据拉取到你的本地仓库，它并不会自动合并或修改你当前的工作。 当准备好时你必须手动将其合并入你的工作。

如果你有一个分支设置为跟踪一个远程分支，可以使用 *git pull* 命令来自动的抓取然后合并远程分支到当前分支。 默认情况下，*git clone* 命令会自动设置本地 master 分支跟踪克隆的远程仓库的master 分支（或不管是什么名字的默认分支）。运行 *git pull* 通常会从最初克隆的服务器上抓取数据并自动尝试合并到当前所在的分支。  

### **推送到远程仓库** ###

使用 *git  push  [remote-name] [branch-name]* 命令可以推送到远程仓库。 当你想要将 master 分支推送到 origin 服务器时（再次说明，克隆时通常会自动帮你设置好那两个名字），那么运行这个命令就可以将你所做的备份到服务器：  

```git 
$ git push origin master
```

只有当你有所克隆服务器的写入权限，并且之前没有人推送过时，这条命令才能生效。  当你和其他人在同一时间克隆，他们先推送到上游然后你再推送到上游，你的推送就会毫无疑问地被拒绝。 你必须先将他们的工作拉取下来并将其合并进你的工作后才能推送。(可先 *pull* 再 *push* )  

### **远程仓库的移除与重命名** ###

如果想要重命名引用的名字可以运行 git remote rename 去修改一个远程仓库的简写名。 例如，想要将 pb 重命名为 paul，可以用 git remote rename 这样做：  

```git 
$ git remote rename pb paul
$ git remote
origin
paul
```

值得注意的是这同样也会修改你的远程分支名字。 那些过去引用 pb/master 的现在会引用 paul/master。  

如果因为一些原因想要移除一个远程仓库，你已经从服务器上搬走了或不再想使用某一个特定的镜像了，又或者某一个贡献者不再贡献了 - 可以使用 *git remote rm* ：  

```git 
$ git remote rm paul
$ git remote
origin
```

基本上学会以上命令完成日常的工作没问题了，少年，加油吧！！！  

**欢迎加QQ群交流： 365532949**  
**Homepage: [http://junkchen.com](http://junkchen.com)**  
