---
layout: post
title: openstack with git
category: coding
description: 2015-03-19

---

Author:[Hyphen](http://weibo.com/344736086)

转载:OpenStack开发过程中常用Git操作场景( by quqi99 )

作者：张华  发表于：2013-07-23
版权声明：可以任意转载，转载时请务必以超链接形式标明文章原始出处和作者信息及本版权声明
[http://blog.csdn.net/quqi99](http://blog.csdn.net/quqi99/article/details/9425385) )

Git是一个分布式的代码管理库，linux之父开发，用了三年多了，直观感受的优点如下：

    一是真正的分布式，既不用担心哪天服务器坏了代码丢失了，也不用担心像中美之间网速慢啊或断网什么的影响开发，因为本地就是一个代码库。
    二是体积小，对存储进行了优化。
    三是速度快，因为比较的是哈希。
    四是差异比较算法很智能，能到达行级，甚至一行中的列级。
    五是支持对二进制的差异比较，如一个Word文档什么的。
    六是其它。。。
    虽然对原理也算熟，用得也算多，但一段时间不用还是容易忘，下面记下一些日常工作场景常用到的命令：

场景一：如何往社区提交一个Bug	    
     
     安装git： sudo yum install git git-review
     下载代码：git clone https://github.com/openstack/neutron.git


1    通过ssh-keygen命令创建密钥, 然后在“https://review.openstack.org/#/settings/ssh-keys 界面设置public key.	
2    运行“git review -s”命令设置git-review, 这步会在.git/config文件中添加一个名为gerrit的远程分支ssh://zhhuabj@review.openstack.org:29418/openstack/neutron.git，并且可以通过ssh访问：ssh -p 29418 review.openstack.org)	
      如果报“Permission denied (publickey)”这样的错的话，简单运行"ssh-add"命令将ssh key添加到ssh agent即可	
还有一种可能是用户弄错了，ssh -i zhhuabj_lcy01.pem ubuntu@162.213.**  -vvv    
3   如果想要修改一个bug时，先创建一个本地分支,	
	
	git checkout -b task/132002
4  写完代码之后，也得运行一下pep8测试吧
      
      pep8 --count --repeat --show-source .  或者     ./run_tests.sh -p -N
5  或者也运行一下单元测试吧
      
      nosetests -s -v test_backend_sql.py  && nosetests -s -v test_db_plugin:TestNetworksV2.test_list_shared_networks_with_non_admin_user
      或者 python -m testtools.run neutron.tests.unit.test_security_groups_rpc
      或者 python setup.py testr --slowest --testr-args='--subunit  neutron.tests.unit.test_security_groups_rpc' |  subunit2pyunit
      或者 ./run_test.sh -N testtools.run neutron.tests.unit.test_security_groups_rpc
6  提交代码到本地库
      
      git add -u (修改的文件）， 若创建了新文件 git add -i
      git commit
   提交信息的格式一般如下，一般分三段，第一段是标准说清楚你要做什么，如果你在改hyper-v模块最好有hyper-v的字眼，这样大家在收到邮件时只看标题就知道你在做什么了，这很重要；第二段简短描述；第三段一般使用“Fixed bug #161317"，＃号很重要，这样会自动在gerrit上链接到bug上。
      
      Add documentation for upgrading hyperv OpenStack compute node

      This guide covers how to upgrade and configure hyperv OpenStack
      compute node from Grizzly to Havana

      Fixed bug #161317
如果是延用上次提交的信息使用命令：	
	
	git commit --amend 
7  本来在git review通过后gerrit会自动rebase到master分支的，但是有冲突的话没人帮你改，所以自己在git review前最好在本地做一个rebase操作，有冲突及时改：
      git rebase master  有冲突再解决冲突之后继续执行：git rebase --continue
8  提交代码评审
      git review 或者 git review master

      这时会在gerrit服务器上创建一个本地分支：refs/for/master/task/132002, 如果上面改bug时不建分支的话，这里就变成了: refs/for/master，下一个人提交直接就被冲掉了。

9 如果社区上的jenkins出错，确定不是你自己的代码造成的，可以回复“recheck no bug” 触发重新检查，或者"recheck bug ###"

10 说说依赖提交，例如如果一个patch在社区已经被很多人+1了，但这时候有人希望提交一个相关的其他patch, 你当然不想破坏这么多已经+1的成果，所以你会希望再提一个patch但它会依赖前一个patch。注意两点：生成一个新的gerrit评审页是由Change-Id决定的；而是否在一个评审页上产生一个新的change set去破坏+1是由commit id决定的。所以只要保证前一个patch的commit id不变就行了，然后在这个基础上再提交一个patch再git review即可。当然，在git review之前最好先git rebase一下，不然如果本地git rebase的话就会产生一个新的commit id这样同样会产生一个新的change set，不过gerrit还比较智能，那些+1会自动添加上（Automatically re-added by Gerrit trivial rebase detection script.），　一个例子见：https://review.openstack.org/#/c/51375/


场景二：如何rebase代码
git rebase用于在一个分支中合并另一分支，举个例子，一家公司想要用OpenStack做二次开发的话，
在公司内部的git库里创建了三个分支：my-master, my-grizzly, my-havana
OpenStack的git库里的分支叫：    github-master, github-grizzly, github-havana
现在大家把内部代码都提交在my-havana分支，那么也需要时不时将社区的github-havana的代码rebase合并到my-havana分支中
1，首先在.git/config里要有两个remote分支，一个指向内部的版本库，一个指向github的版本库，如.git/config的相应配置如下：


	[core]
        repositoryformatversion = 0
        filemode = true
        bare = false
        logallrefupdates = true
	[remote "my-origin"]
        fetch = +refs/heads/*:refs/remotes/my-origin/*
        url = git://<your_ip>/neutron.git
	[remote "github-origin"]
        fetch = +refs/heads/*:refs/remotes/github-origin/*
        url = https://github.com/openstack/neutron.git
	[branch "master"]
        remote = my-origin
        merge = refs/heads/master
	[branch "my-havana"]
        remote = my-origin
        merge = refs/heads/my-havana
	[branch "my-grizzly"]
        remote = my-origin
        merge = refs/heads/my-grizzly
2, 更新上面所有的remote分支 git remote update
3, 因为是要将社区的havana分支的代码同步到自己havana分支，所以为两个远程分支创建对应的本地分支
   git checkout -b github-havana github/stable/havana
   git checkout -b my-havana origin/my-havana
4, 合并，成功后应该用git log -1能看到一个新的合并提交。如果有冲突的话就解决冲突再git merge next
   git checkout my-havana
   git merget github-havana
5, 运行单元测试
  tox --recreate -e py27,pep8 
  ./run_tests.sh -V -f
6, 用-n参数(dry-run)测试提交, 提交到名为my-origin的远程分支的my-havana分支中： 
   git push -n my-origin HEAD:refs/heads/my-havana
   若没有问题的话真正提交：     
   git push  my-origin HEAD:refs/heads/my-havana


场景三：cherry pick代码
例如，一个社区bug 1161195被提交到master分支, 社区没有批准进stable-havana分支，或者来不及等它进，这时候公司内部要求进my-havana分支的话，可以将这个bug通过cherry pick合并过来。
1, 为这个任务创建一个bug.

	git checkout stable-havana	
	git pull	
	git checkout -b bug/1161195	
   	
2, 在社区找到bug 1161195的代码提交id, 如:5f3fa391ed499750ad68ad5b000b4e2e0a86978e	
   可以通过 git log |grep -B 30 <commit-id>查看确认	
3, 在bug/1161195分支上执行cherry pick命令：	
		
	git cherry-pick -x  <commit-id>	
4, 提交代码评审

	git commit or git commit --amend	
	git review stable-havana	



场景四：将git产生的patch变成和svn兼容
	
	#!/bin/sh
	#
	# git-svn-diff
	# Generate an SVN-compatible diff against the tip of the tracking branch
	REV=`git svn find-rev $(git rev-list --date-order --max-count=1 master)`
	git diff --no-prefix $(git rev-list --date-order --max-count=1 master) $* |sed -e 	"s/^+++ .*/& (working copy)/" \
	-e "s/^--- .*/& (revision $REV)/" \
	-e "s/^diff --git [^[:space:]]*/Index:/" -e "s/^index.*/	===================================================================/"



在本机上创建一个共享的git库：

	sudo groupadd git
	sudo useradd -d /home/git -m -g git git
	sudo passwd git
	su - git
	mkdir /home/git/patent.git
	cd patent.git/
	git init --bare --shared

	# test in another directory
	cd /bak/tmp
	git clone git@9.123.136.122:/home/git/patent.git
	cd patent
	cp test.doc .
	git add test.doc
	git commit -m "init commit"
	git push origin master

这时候肯定是希望大家通过git用户可以访问git库，但又不希望他们通过ssh登录这台机器，所以修改/etc/passwd文件，将

	git:x:502:503::/home/git:/bin/bash 

改成：

	git:x:502:503::/home/git:/usr/bin/git-shell

这时候还需要将每个客户端的公钥加到/home/git/.ssh/authorized_keys文件里，公钥可以通过ssh-keygen -t rsa生成。



场景五，使用git命令操作bzr与hg

	sudo apt-get install mercurial python-hglib
	wget https://raw.githubusercontent.com/felipec/git-remote-hg/master/git-remote-hg
	wget https://raw.githubusercontent.com/felipec/git-remote-bzr/master/git-remote-bzr
	sudo chmod 755 ./git-remote-*
	sudo mv git-remote-* /usr/bin

	examples:
	git clone "bzr::lp:ubuntu/ifupdown"
	git clone "bzr::lp:debian/ifupdown"
	git clone "hg::http://anonscm.debian.org/hg/collab-maint/ifupdown/"



场景六，依赖提交

有一种情况，我向社区同时提交了两个有依赖的提交，在本地提交这两个提交之后直接git review即可。
现在前一个提交（假设提交id为：187f）要修改，怎么办呢？
a, git rebase 187f^ --interactive， 回到要修改的提交的前一个点上
b, 修改那个提交，最后git commit --amend
c, git rebase --continue, 继续变基并且返回到原来的HEAD处
如果中间出现“interactive rebase already started”， 用“git rebase -i --abort”命令重来。



其它命令：

	git reset HEAD~1                      撤销最后一次提交。
	git reset --hard HEAD^             撤销最后一次提交并清除本地修改

	git reset --hard Merge_Head  和服务器保持一致

	git clean -dfx                               删除本地未在版本库的东西

	git stash  & git stash list & git stash pop  暂存代码

	git rebase -i --abort      
