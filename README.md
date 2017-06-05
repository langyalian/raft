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
	```

		1 start_working/0
 		2 add_handler/2
 		3 delete_handler/1
        	4 role/0 
	```

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

测试方法：    

	bin/start-master-node 节点名称
	
	bin/start-slave-node  节点名称  主节点名称



例子： 
	bin/start-master-node     node1
	
        bin/start-slave-node	node2    	node1
	
       	bin/start-slave-node	node3    	node1
