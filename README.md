我对reviewboard的一些理解

1、reviewboard的简介

reviewboard是一个开源的代码评审工具，是用Python框架Django开发的，在代码评审方面做的非常优秀。它进行代码评审（codereview）有两种形式：pre-commit-review（代码提交到服务器前进行代码评审）和post-commit-review（代码提交到服务器后进行代码评审）。

reviewboard的pre-commit-review工作模式是这样的：
（1）更改本地代码，创建一个review request；
（2）指定帮你评审代码的人，填写代码评审请求的相关信息，发布你的评审请求；
（3）别人评审你的代码并对你的代码提出评论；
（4）根据评论修改自己的代码并更新之前的代码评审请求直至代码被评审人认可。同时你还可以上传一些截图帮助别人理解你的代码；
（5）评审人认可了你的代码，并给出一个ship it,告诉你可以把代码提交到服务器上了;
（6）把代码提交到服务器上，并在reviewboard上把你的评审请求改成已提交。

reviewboard的post-commit-review工作模式是这样的：
（1）更改本地代码，提交到服务器上；
（2）服务器会对这次的commit产生一个revision号，使用post-review --revision=start version:end version命令创建一个review request，这个命令会触发一个脚本从而diff两个revision之间的差别；
（3）后面的步骤跟pre-commit-review的方式类似。
我个人认为pre-commit-review的方式更好，不容易污染服务器端的代码。总体上reviewboard的功能还是很强大的，在很大程度上提升了程序员的开发效率，节省了程序员的宝贵时间。

同时，reviewboard还提供了一些API文档，如果你是大牛的话，你可以对它的源代码进行完善，当然如果你更改了reviewboard的源代码，那么你仍然需要按照刚才的流程来提交你的代码哦。

2、reviewboard与svn、git的集成使用

在实际的开发过程中，reviewboard必须和版本控制工具结合使用。svn和git是现在主流的两种版本控制工具，svn(subversion的简称)是一个开放源代码的版本控制系统，采用了分支管理系统；git是一款开源的、免费的分布式版本控制系统，它可以更高效的处理项目。
在reviewboard建立一个svn repository时，需要填写以下信息：
Name：项目的名称
Hosting service: 选择Custom
Repository type： 选择Subversion
Path：svn repository地址

在reviewboard中建立一个git repository时，需要填写下列信息：
Name : 项目的名字。
Hosting service: 选择Custom
Repository type： 选择Git
Path： reviewboard server本地下载的git repository路径（如 /var/lib/git/name）
Mirror path：git repository的URL（如 ssh://git@your.server.com/var/lib/git/name）

3、reviewboard中diff的基本原理

在你惊叹reviewboard是如此强大的同时，你也许想知道它是如何生成diff，下面就让我们来揭开它神秘的面纱。
reviewboard中有两种主要的方法生成review request：reviewboard web UI和RBTools的post-review命令。reviewboard web UI的方式，需要上传一个diff文件。但是这个diff文件必须采用统一的diff格式，diff文件需要包含远端服务器文件的相关信息（revision），这个信息跟你采用的版本控制工具有关，在你手动生成diff时，你最好查阅版本控制工具的文档以防止出错。RBTools的post-review方式相对简单些，RBTools是reviewboard官方提供的Python命令行工具，它是用Python API与reviewboard进行交互。在RBTools的一系列命令中，post-review是最重要的命令之一。post-review命令可以用来扫描源代码路径，从而生成diff，并将这个diff上传到一个新的或者已经存在的review request。

rbtools是基于最长公共子序列（LCS）算法生成diff文件的。首先它会比较本地代码和服务器远程代码，如果某一行代码在本地文件中不存在了，而服务器端仍然存在，表示本地删除了这一行代码，删除的代码用'-'标记；如果某一行代码在本地存在，而在服务器端不存在，则表示本地增加了这一行代码，增加的代码用'+'标记。

reviewboard的一个review request会产生一个diff文件，这个diff文件可能会包含很多本地文件与远程文件的diff。diff文件在展示某一个本地文件与远程文件的diff时，会以'---'标记远程文件，而用'+++'标记本地文件。reviewboard中的diff文件格式是上下文格式的，它由一些区块（chunk）组成的，区块列举本地文件和远程文件中的diff。一个区块以'@@'标记开始，直到遇到下一个'@@'结束。举个例子来说，下面是diff文件中某一个本地文件与远程文件的diff：
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

是不是觉得这个diff让人看得眩晕呢？其实在进行code review的时候，我们一般并不直接审查这个diff文件，reviewboard的UI可以帮助我们更清楚地审阅代码，删除的部分，增加的部分以及替换的部分都用不同的颜色高亮，让人一目了然哦。

4、关于reviewboard的一些想法

reviewboard在进行code review的时候十分方便，在review board的dashboard中还可以选择一些column来展示review request的相关信息，包括review request的提交者，发出的时间，最后一次更新的时间，是否有人ship it，代码被review的次数等。

虽然reviewboard的diff UI很不错，但是如果一个团队要对他们的成员的code review进行分析的时候就不太方便了。例如团队的leader想统计团队每一个成员某一段时间内的review request，reviewboard就有点力不从心了，这时就得手动的分析每个成员在这段时间内发的review request的次数；有的时候leader还想知道每次review request中增加的、删除的以及替换的代码量，然而这些信息也不能快捷地得到；如果leader想知道团队里哪个人经常关注团队成员的代码质量，那么就得统计谁经常会review别人的代码或者经常给别人的review request发出一个ship it；还有就是leader想知道哪些人经常不发review request就把代码提交到代码库里了，这时也得手动去统计了。还有一点就是在reviewboard与某些版本控制工具（如git）一块使用的时候，可以将reviewboard的post-review命令与git commit结合使用，当使用git commit命令时会自动触发一个脚本执行post-review命令，那么开发人员就不需要手动地再输入post-review命令了，希望reviewboard以后的版本能在将来做的更好。当然以上是我自己的一些建议，如果有什么不妥之处，敬请指出。
