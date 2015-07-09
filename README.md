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

git是一款开源的、免费的分布式版本控制系统，它可以更高效的处理项目。在reviewboard中建立一个git repository时，需要填写下列信息
Name : 项目的名字。
Hosting service: 选择Custom
Repository type： 选择Git
Path： reviewboard server本地下载的git repository路径（如 /var/lib/git/name）
Mirror path：git repository的URL（如 ssh://git@your.server.com/var/lib/git/name）

3、reviewboard中diff的基本原理

4、我对reviewboard中数据分析的一些看法
