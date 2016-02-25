# Git 基础

##获取 Git 仓库
###在现有目录初始化存储库  
进入需要被追踪的项目目录并执行：

    $git init
初始化存储仓库会建立名为 `.git`的子目录，该目录包一个 Git 存储库架构所必要的所有文档。此时项目内任何额文档都还没有被追踪。  

对现有的文档开始做版本控制（除了空的目录以外），应该追踪这些文档并做第一次提交。可使用 git add 命令指定要追踪的文档，并提交。  

    $git add *.c
    $git add README
    $git commit -m 'initial project version'

##复制现有的版本库
Git 拉取的是服务器端所有资料的复本，项目历史中所有文档的所有版本都在执行 `git clone` 后拉回到本地。  

    $git clone https://github.com/morro-wind/gitshare.git mygit
会在当前路径下建立名为 `mygit` 的目录，在并其下初始化名为 `.git` 的目录。拉取版本库的所有文件，并拉取最新版本为工作复本。  

#提交更新到仓库
工作目录下的每个文件不外乎两正状态：  
已跟踪：指那些被纳入了版本控制的文件，上一次快照有其记录，现在状态可能处于未修改、已修改或已放入暂存区。  
未跟踪：除已跟踪文件以外都属未跟踪文件。  

编辑过某些文件后，由于自上次提交后对它们做了修改，Git 将它们标记为已修改文件。将这些修改的文件放入暂存区，然后提交所有暂存了的修改，如此反复。所以 Git 文件的生命周期如下：  

![](img/2-1.png)  

##查看文件当前状态  
使用 `git status` 命令可以查看文件处于什么状态。  

    $ git status
    On branch master
    nothing to commit, working directory clean
 
 Working directory clean 说明目前的工作目录没有未被跟踪或已被修改的文件。如有未被跟踪文档，Git 会将它们列出。同显示目前在哪一个分支（branch）。  
 
 新增文档到工作区后，再执行`git status`命令，会看到未被跟踪的档案  
 
    $touch README
    $git status
    On branch master
    Untracked files:
      (use "git add <file>..." to include in what will be committed)

            README

    nothing added to commit but untracked files present (use "git add" to track)

Untracked files 下发显示的就是未被跟踪的文件。除非明确指定要将文件加入提交的快照，Git 不会主动将它加入。

##跟踪新文件
使用 `git add` 命令，跟踪新增文件：

    $git add README
再次查看文件状态,已被列入跟踪并且已暂存：  

    $git status
    On branch master
    Changes to be committed:
      (use "git reset HEAD <file>..." to unstage)
    
            new file:   README
Changes to be commited下方的是已暂存的文件。此时提交更新，则被暂存的文件就会被记录在历史快照中。  

##暂存已修改文件
修改已被跟踪的文件 `benchmarks.rb`,并查看文件状态：

    $git status
    On branch master
    Changes to be committed:
     (use "git reset HEAD <file>..." to unstage)
    
            new file:   README

    Changes not staged for commit:
      (use "git add <file>..." to update what will be committed)
      (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   benchmarks.rb

***"Changes not staged for commit"***下发文件代表着文件已被跟踪且已被修改，但尚未暂存。暂存可执行`git add`命令（这是一个多用途的指令）。   

将已暂存的`benchmarks.rb`再做一些修改后提交，再查看文件状态：

    $git status
    On branch master
    Changes to be committed:
      (use "git reset HEAD <file>..." to unstage)

            new file:   README
            modified:   benchmarks.rb

    Changes not staged for commit:
      (use "git add <file>..." to update what will be committed)
      (use "git checkout -- <file>..." to discard changes in working directory)

            modified:   benchmarks.rb
            
`benchmarks.rb`同时被列在已被暂存及未被暂存。现在提交更新，则最近一次执行`git add`命令时暂存的`benchmarks.rb`会被提交。若在`git add`后修改文件，则需要修改后再次执行`git add`将最新版的文件暂存起：  

    $ git add benchmarks.rb
    $ git status
    On branch master
    Changes to be committed:
      (use "git reset HEAD <file>..." to unstage)
    
            new file:   README
            modified:   benchmarks.rb
##忽略文件
通常会有一些文件不想让Git自动新增，也不希望被列入未被跟踪的文件。这些文件通常是自动生成的。将这些文件名写入`.gitignore`文件：  

    $ cat .gitignore
    *.[oa]
    *~
第一列告诉Git忽略任何文件名为`.o`或`.a`结尾的文件。第二列告诉Git忽略所有文件名为`~`结尾的文件。  

编写`.gitignore`文件的规则：  
* 空白列或者以#开头的列会被忽略
* 可使用标准的Glob pattern
* 可以/结尾，代表是目录
* 可使用!符号取反  

另一个`.gitignore`范例：  

    # 註解，會被忽略。
    # 不要追蹤檔名為 .a 結尾的檔案
    *.a
    # 但是要追蹤 lib.a，即使上方已指定忽略所有的 .a 檔案
    !lib.a
    # 只忽略根目錄下的 TODO 檔案。 不包含子目錄下的 TODO
    /TODO
    # 忽略build/目錄下所有檔案
    build/
    # 忽略doc/notes.txt但不包含doc/server/arch.txt
    doc/*.txt
    # ignore all .txt files in the doc/ directory
    doc/**/*.txt

##查看修改
`git diff` 可以查看尚未暂存的文件更新了哪些部分。此命令比较的是工作目录中当前文件和暂存区快照之间的差异，也就是暂存起来的变化内容。  

    $ git diff
    diff --git a/CONTRIBUTING.md b/CONTRIBUTING.md
    index 8ebb991..643e24f 100644
    --- a/CONTRIBUTING.md
    +++ b/CONTRIBUTING.md
    @@ -65,7 +65,8 @@ branch directly, things can get messy.
     Please include a nice description of your changes when you submit your PR;
     if we have to read the whole diff to figure out why you're contributing
     in the first place, you're less likely to get feedback and have your change
    -merged in.
    +merged in. Also, split your changes into comprehensive chunks if your patch is
    +longer than a dozen lines.
    
     If you are starting to work on a particular area, feel free to submit a PR
     that highlights your work in progress (and note in the PR title that it's

若要查看已暂存的将要添加到下次提交里的内容，可以使用`git diff --cached`命令。  
`git diff`本身只显示尚未暂存的改动。

##提交更系
在提交之前请一定要确认还有什么修改过的或新建的文件还没有`git add`过，否则提交的时候不会记录这些还没暂存起来的变化。每次准备提交前，先用`git status`看下，是否都已暂存起来了，然后再执行`git commit`：  

    $git commit
    
    # Please enter the commit message for your changes. Lines starting
    # with '#' will be ignored, and an empty message aborts the commit.
    # On branch master
    # Changes to be committed:
    #       new file:   README
    #       modified:   benchmarks.rb
    #
    ~
    ~
    ~
    ".git/COMMIT_EDITMSG" 10L, 283C
开头有一行空行，供输入提交说明。可使用`-m` 参数指定提交信息：

    $ git commit -m "Story 182: Fix benchmarks for speed"
    [master 463dc4f] Story 182: Fix benchmarks for speed
     2 files changed, 3 insertions(+)
     create mode 100644 README

现在已建立地一个提交。输出信息可看出此提交、放到哪个分支（master）、SHA-1校验码（463dc4f），以及本次提交中，有多少文件修订过，多少行添加和删除过。

提交时记录的是放在暂存区域的快照。任何未暂存的仍然保持已修改状态。

##跳过使用暂存区域
使用`git commit -a`可跳过暂存区域，Git会自动把所有已经跟踪过的文件暂存起来一并提交从而跳过`git add`这步

    $ git status
    On branch master
    Changes not staged for commit:
      (use "git add <file>..." to update what will be committed)
      (use "git checkout -- <file>..." to discard changes in working directory)
    
        modified:   CONTRIBUTING.md
    
    no changes added to commit (use "git add" and/or "git commit -a")
    $ git commit -a -m 'added new benchmarks'
    [master 83e38c7] added new benchmarks
     1 file changed, 5 insertions(+), 0 deletions(-)

##移除文件
Git移除文件需要从已跟踪文件清单中移除（暂存区），然后提交。可以使用`git rm`命令，并连带从工作目录中删除指定的文件  
将文件从Git仓库中删（从暂存区移除），但保留在留在工作目录中，可使用`--cached`选项

    $git rm --cached README
##移动文件
`git mv`可以重命名文件：

    $git mv file_from file_to
`git mv`相当于运行了下面三条命令：

    $mv README.md README
    $git rm README.md
    $git add README

##查看提交历史
`git log`命令可以查看项目的提交历史

    $ git log
    commit ca82a6dff817ec66f44342007202690a93763949
    Author: Scott Chacon <schacon@gee-mail.com>
    Date:   Mon Mar 17 21:52:11 2008 -0700
    
        changed the version number
    
    commit 085bb3bcb608e1e8451d4b2432f8ecbe6306e7e7
    Author: Scott Chacon <schacon@gee-mail.com>
    Date:   Sat Mar 15 16:40:33 2008 -0700
    
        removed unnecessary test
    
    commit a11bef06a3f659402fe7563abf99ad00de2209e6
    Author: Scott Chacon <schacon@gee-mail.com>
    Date:   Sat Mar 15 10:31:28 2008 -0700
    
        first commit

不用任何参数的话，`git log`会按提交时间列出所有的更新，最近的更新排在最上面。

-p 用来显示每次提交内容的差异。你也可以加上-2来显示最近两次提交：

    $ git log -p -2
    commit ca82a6dff817ec66f44342007202690a93763949
    Author: Scott Chacon <schacon@gee-mail.com>
    Date:   Mon Mar 17 21:52:11 2008 -0700
    
        changed the version number
    
    diff --git a/Rakefile b/Rakefile
    index a874b73..8f94139 100644
    --- a/Rakefile
    +++ b/Rakefile
    @@ -5,7 +5,7 @@ require 'rake/gempackagetask'
     spec = Gem::Specification.new do |s|
         s.platform  =   Gem::Platform::RUBY
         s.name      =   "simplegit"
    -    s.version   =   "0.1.0"
    +    s.version   =   "0.1.1"
         s.author    =   "Scott Chacon"
         s.email     =   "schacon@gee-mail.com"
         s.summary   =   "A simple gem for using Git in Ruby code."
    
    commit 085bb3bcb608e1e8451d4b2432f8ecbe6306e7e7
    Author: Scott Chacon <schacon@gee-mail.com>
    Date:   Sat Mar 15 16:40:33 2008 -0700
    
        removed unnecessary test
    
    diff --git a/lib/simplegit.rb b/lib/simplegit.rb
    index a0a60ae..47c6340 100644
    --- a/lib/simplegit.rb
    +++ b/lib/simplegit.rb
    @@ -18,8 +18,3 @@ class SimpleGit
         end
    
     end
    -
    -if $0 == __FILE__
    -  git = SimpleGit.new
    -  puts git.show
    -end
    \ No newline at end of file

git log 常用选项

    选项 	            说明

    -p               按补丁格式显示每个更新之间的差异。

    --stat           显示每次更新的文件修改统计信息。
    
    --shortstat      只显示 --stat 中最后的行数修改添加移除统计。
    
    --name-only      仅在提交信息后显示已修改的文件清单。
    
    --name-status    显示新增、修改、删除的文件清单。
    
    --abbrev-commit  仅显示 SHA-1 的前几个字符，而非所有的 40 个字符。
    
    --relative-date  使用较短的相对时间显示（比如，“2 weeks ago”）。
    
    --graph          显示 ASCII 图形表示的分支合并历史。
    
    --pretty         使用其他格式显示历史提交信息。可用的选项包括 oneline，short，full，fuller 和 format（后跟指定格式）。


    
