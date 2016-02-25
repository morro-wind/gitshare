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


