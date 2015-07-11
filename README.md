我对reviewboard的一些理解

1、reviewboard的简介

reviewboard是一个开源的代码评审工具，是用Python框架Django开发的。它在审查本地文件与远程文件的diff上做的非常优秀。它进行代码评审（codereview）有两种形式：pre-commit-review（代码提交到服务器前进行代码评审）和post-commit-review（代码提交到服务器后进行代码评审）。pre-commit-review是这样工作的：如果你更改了本地文件，那么reviewboard可以很好地帮助程序员发出一个评审代码的请求，你可以指定帮你评审代码的人，别人可以评审你的代码并对你的代码提出评论，然后你可以根据评论修改自己的代码并再次发出一个评审代码的请求直至代码被评审人认可。同时你还可以上传一些截图帮助别人理解你的代码。如果你的代码被评审人认可，那么他会给你发一个通知，告诉你可以把代码提交到服务器上了，如果你把代码提交到了服务器上，那么你可以在reviewboard上把你的评审请求改成已提交。这一系列流程在很大程度上提升了程序员的开发效率，节省了程序员的宝贵时间。

同时，reviewboard还提供了一些API文档，如果你是大牛的话，你可以对它的源代码进行完善，当然如果你更改了reviewboard的源代码，那么你仍然需要按照刚才的流程来提交你的代码哦。

2、reviewboard与SVN、git的集成使用

svn(subversion的简称)是一个开放源代码的版本控制系统，采用了分支管理系统。在reviewboard建立一个svn repository时，需要填写以下信息：
Name：项目的名称
Hosting service: 选择Custom
Repository type： 选择Subversion
Path：服务器中对应的repository地址

git是一款开源的、免费的分布式版本控制系统，它可以更高效的处理项目。在reviewboard中建立一个git repository时，需要填写下列信息：
Name : 项目的名字。
Hosting service: 选择Custom
Repository type： 选择Git
Path： reviewboard server本地下载的git repository路径（如 /var/lib/git/name）
Mirror path：git repository的URL（如 ssh://git@your.server.com/var/lib/git/name）

3、reviewboard中diff的基本原理

reviewboard中有两种主要的方法生成一个review request：通过web UI和rbtools 的post-review命令。后者的方法使用的居多，因为它内部为你做了很多其他的工作，你只需输入几行命令就可以了。如果采用web UI的方式，你需要上传一个diff文件。这个diff文件必须采用统一的diff格式，diff文件需要包含远端服务器文件的信息，这个信息跟你采用的版本控制系统有关，在你手动生成diff时，你最好查阅版本控制系统的文档以防止出错。

RBTools是一系列命令行工具的集合，其中post-review是最重要的命令之一。post-review命令可以用来扫描源代码路径，从而生成diff，并将这个diff上传到一个新的或者已经存在的review request。

rbtools是基于最长公共子序列（LCS）算法生成diff文件的。首先它会比较本地代码和服务器远程代码，如果某一行代码在本地文件中不存在了，而服务器端仍然存在，表示本地删除了这一行代码，删除的代码用'-'标记；如果某一行代码在本地存在，而在服务器端不存在，则表示本地增加了这一行代码，增加的代码用'+'标记。

reviewboard的一个review request会产生一个diff文件，这个diff文件可能会包含很多本地文件与远程文件的不同。diff文件在展示某一个本地文件与远程文件的不同时，会以'---'标记远程文件，而用'+++'标记本地文件。reviewboard中的diff文件格式是上下文格式的，它由一些区块（chunk）组成的，区块列举本地文件和远程文件中不同的地方。一个区块以'@@'标记开始，直到遇到下一个'@@'结束。举个例子来说，下面是diff文件中某一个本地文件与远程文件的diff：
--- /local/path/test.txt
+++ /server/path/test.txt
@@ -400,8 +397,7 @@ test0
test1
test2
-delete1
-delete2
test3
test4
+add1
test5
test6
其中'-400,8'表示展示这段diff时，远程文件是从该文件的第400行开始的，共展示8行；'+400,7'表示本地文件是从第397行开始的，共展示7行。在每个区块中，以'-'开头表示本地文件删除了该行，以'+'开头表示本地文件增加了该行。

4、我对reviewboard中数据分析的一些看法
