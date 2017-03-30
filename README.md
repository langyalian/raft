raft 主要实现目标是在集群中选出一个领导者

系统一共有三个角色 :	
			1 追随者（follower）
			2 候选人（candidate）
			3 领导者 （leader）

角色转换：
		1 追随者可以转换为候选人
		2 候选人可以转换为追随者和领导人
		3 领导人可以转换为追随者


接口使用说明：
	raft模块提供了4个接口：

		1 start_working/0
 		2 add_handler/2
 		3 delete_handler/1
        4 role/0 

下面对每个接口进行描述：
	1 start_working/0
	在使用raft应用时必须调用一次 start_working/0，否则不会进行正常工作。

	2 add_handler/2
	 该函数实现事件注册功能，在节点角色发生转变时会调用该函数。
	 参数一：事件处理器标识
	 参数二：fun/1, 函数中的参数为事件类型 'become_leader'|'become_follower'

	3 delete_handler/1
	  该函数实现取消注册的事件处理函数
	  参数一：add_handler/2函数中传递的事件处理器标识

	 4 role/0 
	 	该函数可以用来查看当前节点角色类型 'follower'|'candidate'|'leader'




测试用例：

编译命令：    $ ./rebar3 compile

测试方法：    $ bin/start-master-node 节点名称
			bin/start-slave-node  节点名称  主节点名称



例子： bin/start-master-node	node1
       bin/start-slave-node	node2    	node1
       bin/start-slave-node	node3    	node1




运行情况如下
	在关掉节点一后节点三成为了领导者

节点一：

raft$ bin/start-master-node node1
Erlang/OTP 18 [erts-7.3] [source] [64-bit] [smp:4:4] [async-threads:10] [hipe] [kernel-poll:false]

Eshell V7.3  (abort with ^G)
(node1@127.0.0.1)1> 15:38:37.408 [info] Application lager started on node 'node1@127.0.0.1'
15:38:37.410 [info] Application raft started on node 'node1@127.0.0.1'
15:38:37.413 [info] [raft 234] start master node ('node1@127.0.0.1') success

(node1@127.0.0.1)1> nodes().
['node2@127.0.0.1','node3@127.0.0.1']
(node1@127.0.0.1)2> raft:role().
leader
(node1@127.0.0.1)3> q().
ok



节点二：

raft$ bin/start-slave-node node2    node1
Erlang/OTP 18 [erts-7.3] [source] [64-bit] [smp:4:4] [async-threads:10] [hipe] [kernel-poll:false]

Eshell V7.3  (abort with ^G)
(node2@127.0.0.1)1> 15:38:41.315 [info] Application lager started on node 'node2@127.0.0.1'
15:38:41.317 [info] Application raft started on node 'node2@127.0.0.1'
15:38:41.324 [info] [raft 246] cluster node 'node1@127.0.0.1' success
15:38:41.332 [info] [raft 249] start   node ('node2@127.0.0.1') success
  
(node2@127.0.0.1)1> nodes().
['node1@127.0.0.1','node3@127.0.0.1']
(node2@127.0.0.1)2> raft:role().
follower
(node2@127.0.0.1)4> nodes().
['node3@127.0.0.1']
(node2@127.0.0.1)3> raft:role().
follower
(node2@127.0.0.1)5> 

节点三：

raft$ bin/start-slave-node node3    node1
Erlang/OTP 18 [erts-7.3] [source] [64-bit] [smp:4:4] [async-threads:10] [hipe] [kernel-poll:false]

Eshell V7.3  (abort with ^G)
(node3@127.0.0.1)1> 15:38:44.923 [info] Application lager started on node 'node3@127.0.0.1'
15:38:44.926 [info] Application raft started on node 'node3@127.0.0.1'
15:38:44.930 [info] [raft 246] cluster node 'node1@127.0.0.1' success
15:38:44.934 [info] [raft 249] start   node ('node3@127.0.0.1') success

(node3@127.0.0.1)1> nodes().
['node1@127.0.0.1','node2@127.0.0.1']
(node3@127.0.0.1)2> raft:role().
follower
(node3@127.0.0.1)4> nodes().
['node2@127.0.0.1']
(node3@127.0.0.1)3> raft:role().
leader
(node3@127.0.0.1)5>
