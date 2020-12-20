# Git
## 基本操作
### 初始化
- 本地初始化：`git init`
- 从服务端复制项目：`git clone <server url>`

### 文件状态
![文件状态变化](https://git-scm.com/book/en/v2/images/lifecycle.png)
- 查看项目状态：`git status [-s/--short以简洁方式显示]`

### 添加文件
- 添加：`git add <file name>`将文件添加到下一次提交中，即把状态变为下下一个：将文件添加到git(从untracked到tracked)，或修改后将改动添加到准备提交状态(从modified到staged)
### 忽略文件
使用`.gitignore`文件添加忽略文件。
- 忽略后缀名为"xx"的文件：`*.xx`
- 忽略某一类型文件后，需要保持"someFile.xx"文件：`!someFile.xxx`
- 忽略所有名为"someDir"的文件夹：`someDir/`
- 只忽略当前目录下的"someDir"文件夹：`/someDir`
- 忽略"someDir"文件夹内不含子文件夹的所有后缀为"xx"的文件：`someDir/*.xx`
- 忽略"someDir"文件夹内及其子文件夹下所有后缀为"xx"的文件：`someDir/**/*.xx`

### 文件对比
- `git diff`：查看工作目录中当前文件和暂存区域之间的差异，也就是修改之后还没有暂存起来的变化内容。
- `git diff --staged/--cached`：查看暂存文件与最后一次提交的文件差异。
- `git difftool`：调用emerge或vimdiff等软件（包括商业软件）输出 diff 的分析结果。使用`git difftool --tool-help`来看你的系统支持哪些GitDiff插件。

### 提交文件
- 提交文件：`git commit [-m "提交信息"]`
注意，将文件提交前，需要使用`git add`来将要提交的文件添加到待提交状态。
- 将文件添加并提交：`git commit -a [-m "提交信息"]`这条命令相当于先执行`git add`再执行`git commit`.

### 移除文件
如果直接删除文件，那么当前文件的状态为从文件系统中被删除，但对于git来说，相当于文件发生了变动，要进行提交则必须进行stage再commit.

- 从git中删除文件并从文件系统中删除：`git rm [-f(当文件被修改过但需要删除时，使用-f强制删除)] <文件名>`文件名也可以使用通配符，注意*需要转义，如：`git rm someDir/\*.xx`
- 从git中删除文件但不从文件系统中删除：`git rm [--cached(当文件已修改且要保存想改的内容时)] <文件名>`此时无论文件是否已提交都会从git中删除，但在文件系统中保留，即untracked.如果是不小心add的文件要从git删除，建议使用[撤消操作](#撤消操作)`git rm --cached <file>`.

### 重命名或移动文件
`git mv <oldFile> <newFile>`
这相当于执行了以下三条命令：
```
mv oldFile newFile
git rm oldFile
git add newFile
```
### 查看提交历史
`git log`可以用来查看git的提交历史，其后可添加多个参数：
- 显示每次提交的差异：`-p`或`--path`
- 显示每次提交的统计信息（增加、删除、修改了多少个文件）：`--stat`
- 格式化输出效果：`--pretty`
如：`--pretty=oneline`将每个提交放在一行显示，此外还有还有`short`，`full`和`fuller`选项。
重要的，`--pretty=format`以指定的格式显示：如`git log --pretty=format:"%h - %an, %ar : %s"`

|选项|说明|
|:-:|:-|
|%H|提交的完整哈希值|
|%h|提交的简写哈希值|
|%T|树的完整哈希值| 
|%t|树的简写哈希值| 
|%P|父提交的完整哈希值| 
|%p|父提交的简写哈希值| 
|%an|作者名字| 
|%ae|作者的电子邮件地址| 
|%ad|作者修订日期（可以用 --date=选项 来定制格式）| 
|%ar|作者修订日期，按多久以前的方式显示| 
|%cn|提交者的名字| 
|%ce|提交者的电子邮件地址| 
|%cd|提交日期| 
|%cr|提交日期（距今多长时间）| 
|%s|提交说明|

`git log`的常用选项:
|选项|说明|
|:-:|:-|
|-p|按补丁格式显示每个提交引入的差异。| 
|--stat|显示每次提交的文件修改统计信息。| 
|--shortstat|只显示 --stat 中最后的行数修改添加移除统计。| 
|--name-only|仅在提交信息后显示已修改的文件清单。| 
|--name-status|显示新增、修改、删除的文件清单。| 
|--abbrev-commit|仅显示 SHA-1 校验和所有 40 个字符中的前几个字符。| 
|--relative-date|使用较短的相对时间而不是完整格式显示日期（比如“2 weeks ago”）。| 
|--graph|在日志旁以 ASCII 图形显示分支与合并历史。| 
|--pretty|使用其他格式显示历史提交信息。可用的选项包括 oneline、short、full、fuller 和 format（用来定义自己的格式）。|
|--oneline|--pretty=oneline --abbrev-commit 合用的简写。|

- 限制输出的条数：`-n（n为数字）`
- 按照时间作限制：`--since`和`--until`
- 过滤包含某一字符串的提交：`git log -S "xxx"`，可用于搜索某个方法或类的提交记录

限制`git log`输出的选项
|选项|说明|
|:-:|:-|
|-n|仅显示最近的 n 条提交。| 
|--since, --after|仅显示指定时间之后的提交。| 
|--until, --before|仅显示指定时间之前的提交。| 
|--author|仅显示作者匹配指定字符串的提交。| 
|--committer|仅显示提交者匹配指定字符串的提交。| 
|--grep|仅显示提交说明中包含指定字符串的提交。| 
|-S|仅显示添加或删除内容匹配指定字符串的提交。|

### 撤消操作
- commit后追加提交：`git commit --amend [-m]`将产生一条提交记录
- 恢复文件的git状态：`git reset HEAD <file>`将文件在git中的状态恢复到HEAD也就是上一次commit的状态。可以用来恢复`git add`的状态，可将已提交且修改并`git add`保存后的文件恢复到`git add`之前的状态，即not staged，或将`git add`刚添加到git的文件恢复到untrack状态(此时也可以使用`git rm --cached <file>`)。
- 撤消对文件的修改：`git checkout -- <file>`用最近提交的版本覆盖该文件。但如果使用`git rm`将文件删除后，`checkout`将无法恢复文件，而`reset`可恢复到上一次commit的状态。

### 远程仓库
- 查看远程仓库：`git remote -v`
- 添加一个新的远程仓库：`git remote add <shortname> <url>`
- 查看某一个远程仓库的信息：`git remote show <shortname>`
- 重命名一个远程仓库的简写名：`git remote rename <oldName> <newName>`
- 移除一个远程仓库：`git remote remove <name>`或`git remote rm <remote>`
- 从远程仓库中抓取与拉取：`git fetch <remote>`(不进行merge)和`git pull <remote>`(自动进行merge)。
- 推送到远程仓库：`git push <remote> <branch>`.

### 标签
Git支持两种标签:轻量标签(lightweight)与附注标签(annotated)。轻量标签很像一个不会改变的分支——它只是某个特定提交的引用。而附注标签是存储在Git数据库中的一个完整对象，它们是可以被校验的。

- 列出已有的标签：`git tag`
- 根据过滤条件列出标签：`git tag -l/--list "V1.3.*"`
- 查看标签信息和与之对应的提交信息：`git show <tagName>`
- 添加轻量标签：`git tag <tagName> [-m "message"]`
- 添加附注标签：`git tag -a <tagName> [-m "message"]`
- 在已提交的记录中打标签：`git tag [-a] <tagName> <commitHash>`
- 上传标签：`git push <remote> <tagName>`
- 上传本地所有标签：`git push <remote> --tags`
- 删除本地标签：`git tag -d <tagName>`
- 删除远程标签：`git push origin --delete <tagName>`或`git push <remote> :refs/tags/<tagName>`

### 别名
觉得git命令太长不好输入时，可以通过git config来配置git命名的别名，如`git config --global alias.ci commit`，之后，可以输入`git ci`相当于`git commit`

## 分支
当使用`git commit`进行提交操作时，Git会先计算每一个子目录的校验和， 然后在Git仓库中这些校验和保存为树对象。随后，Git便会创建一个提交对象，它除了包含上面提到的那些信息外，还包含指向这个树对象(项目根目录)的指针。如此一来，Git就可以在需要的时候重现此次保存的快照。

之后，Git仓库中存在着：N个blob对象(保存着文件快照)、一个tree对象(记录着目录结构和blob对象索引)以及一个commit对象(包含着指向前述树对象的指针和所有提交信息)。

再次做出提交，产生的提交对象会包含一个指向上次提交对象(父对象)的指针。

Git创建分支，只是创建了一个可以移动的新的指针。Git中有一个名为HEAD的特殊指针，指向当前所在的分支。

### 本地分支
- 创建分支：`git branch <branchName>`
- 切换分支：`git checkout <branchName>`
- 创建新分支的同时切换过去：`git checkout -b <newBranchName>`
- 合并分支：先切换到最终要操作的分支，`git merge <branchName>`将指定的分支合并到当前分支。
Git合并的结果是做了一个新的快照并且自动创建一个新的提交指向它。这是一次合并提交，它的特别之处在于他有不止一个父提交。
当合并存在冲突时，需要手动解决冲突，可使用`git status`查看冲突，解决后使用`git add`及`git commit`再次提交。此外，还可以使用`git mergetool`启动可视化工具合并冲突。
- 查看分支状态：`git branch`
- 查看每一个分支的最后一次提交：`git branch -v`
- 过滤已经合并或尚未合并到当前分支的分支：`git branch --merged/--no-merged`
- 查看分支的分叉历史：`git log --oneline --decorate --graph --all`
- 删除本地分支：`git branch -d <localBranchName>`当有代码未合并时会删除失败，若要强行删除可以使用`-D`来代替`-d`。

### 远程分支
从远程分支`git fetch`拉取代码，本地git仓库将会拉取代码，并将`remote/branch`的指针指向其位置：

![git fetch](https://git-scm.com/book/en/v2/images/remote-branches-3.png)

- 推送当前分支到远程分支：`git push <remote> <remoteBranchName>`
- 推送本地其他分支到远程分支：`git push <remote> <localBranchName>:<remoteBranchName>`
- 从远程分支拉取代码在本地创建分支：`git checkout -b <localBranchName> <remote>/<remoteBranchName>`当本地分支与远程分支名称保持一致时可以简化为`git checkout --track <remote>/<remoteBranchName>`，当远程仓库只有一个时，更进一步简化为：`git checkout <remoteBranchName>`
- 设置已有的本地分支跟踪远程分支或修改跟踪的远程分支：`git branch -u(等同于--set-upstream-to) <remote>/<remoteBranchName>`
- 合并远程分支到本地分支：`git merge <remote>/<remoteBranchName>`或`git merge @{u}`或`git merge @{upstream}`
- 查看本地分支与远程分支之间的关系及差距：`git branch -vv`
- 删除远程分支：`git push <remote> --delete <remoteBranchName>`

### 变基
变基rebase也是用来整合不同的分支，它可以将提交到某一分支上的所有修改都移至另一分支上。不同于merge之处在于，提交历史是一条直线没有分叉。

将dev分支变基到master：
```
# 1. 切到dev分支
git checkout dev
# 2. 将dev变基到master
git rebase master
# 1-2. 以上两个命令可以不用切换dev分支，使用以下命令
git rebase master dev
# 3. 切到master
git checkout master
# 4. 将dev变基后合并到master
git merge dev
```

变基的重要注意点：最好只在本地仓库中变基，不要在远程仓库中变基。

如果A拉取了远程仓库，而B之后在远程仓库中执行了变基，那么A再次拉取代码并合并，变基前的操作就又会恢复。可以使用`git pull --rebase`来修复。此外，还可以使用`git config --global pull.rebase true`来更改`pull.rebase`的默认配置，使用`git pull`可以默认加上`--rebase`.

当rebase发生冲突时，需要手动解决冲突并使用`git rebase --continue`继续，或可以使用`git rebase --abort`取消rebase.

## 远程仓库
- 创建远程git仓库：创建`xxx.git`文件夹，并使用`git init --bare`初始化
- 通过`git remote add <name> <localPath>`来添加本地仓库和远程仓库的关联，通过`--set-upstream`设置两者的关联，就可以与远程仓库交互了。
- 生成ssh密钥：`ssh-keygen [-t rsa（不使用-t时默认就是rsa类型）] -C "email@email.com" -f <fileName>`

### 其他
- 比较当前分支与指定分支的不同：`git diff <branchName>`
- 查看两个分支的公共父结点：`git merge-base <branch1> <branch2>`
- 查看两个分支基于公共父结点的不同：`git diff <branch1>...<branch2>`
- 拣选提交的代码：`git cherry-pick <commitHash>`
- 生成构建号：`git describ <branch>`会生成一个字符串， 它由最近的标签名、自该标签之后的提交数 目和你所描述的提交的部分 SHA-1 值(前缀的 g 表示 Git)构成，如：v1.6.2-rc1-20-g8c5b85c
- 发布tar.gz：`git archive master --prefix='project/' | gzip > `\`git describe master\``.tar.gz`生成如：v1.6.2-rc1-20-g8c5b85c.tar.gz
- 发布zip：`git archive master --prefix='project/' --format=zip > `\` git describemaster\``.zip`

## Git工具
### 查看引用
- 查看分支指向哪个特定的SHA-1：`git rev-parse <branch>`
- 查看引用日志（记录了最近HEAD和分支引用所指向的历史）：`git reflog`
- 查看某分支在某时指向了哪个提交：`git show <branch>@{<time>}`其中`<time>`可以为`yesterday`,`2.months.ago`等格式
- 查看一个引用的上一个提交（祖先引用）：`git show <branch>^`在windows上需要使用两个`^^`或放入引号：`"<branch>^"`，
还可以使用`git show <branch>~`，但`~`可以使用多个或后边增加数字，如`git show HEAD~~~`和`git show HEAD~3`可以查看当前提交的之前第3次提交。
- 选出在一个分支中而不在另一个分支中的提交，比如在提交前可以查看在HEAD中而不在origin/master中的提交：
```
// 在branch2中而不在branch1中
git log <branch1>..<branch2>
git log ^<branch1> <branch2>
git log <branch2> --not <branch1>

// 在branch1和branch2中而不在branch3中
git log <branch1> <branch2> --not <branch3>

// 在两个分支之一中存在，但不是两者共有的提交
git log <branch1>...<branch2>

// 在两个分支之一中存在，但不是两者共有的提交，并显示每个提交到底处于哪一侧的分支
git log --left-right <branch1>...<branch2>
```

### 暂存stash
- 进入一个交互式终端模式：`git add -i/--interactive`
- 想要切换分支，但是还不想要提交之前的工作;所以贮藏修改：`git stash`
- 查看暂存的列表：`git stash list`
- 将刚刚贮藏的工作重新应用: `git stash apply`
- 将指定贮藏的工作重新应用：`git stash apply <stashName>如stash@{2}`
- 删除最近的一次贮藏：`git stash pop`
- 删除指定的贮藏：`git stash drop <stashName>`
- 贮藏未跟踪的文件：`git stash -u/--include-untracked`
- 贮藏包括已忽略的文件：`git stash -a/--all`
- 从贮藏创建一个分支：`git stash branch <new branchname>`
- 去除冗余文件或者清理工作目录：`git clean -f`移除工作目录中所有未追踪的文件
- 查看将删除哪些文件：`git clean -n/--dry-run`
- 清理文件夹：`git clean -d -f`