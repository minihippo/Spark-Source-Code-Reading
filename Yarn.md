# Yarn

motivation：划分资源管理和作业调度为两个独立的部分（hadoop1.x是合在一起JobTracker），为了不仅仅只支持MR任务，还支持Hive、Spark等应用

idea：一个全局的ResourceManager；每个应用一个applicationMaster

### architecture

<img src='https://github.com/minihippo/Spark-Source-Code-Reading/tree/branch-notes/img/yarn', style='zoom:80%'>

- **ResourceManager**：为所有应用分配资源

  - Scheduler：按调度策略基于Container给application分配资源。调度策略可插拔，常用的有fairScheduler(队列)、CapacityScheduler(capacities)。

  - ApplicationsManager：接收job的提交请求，为应用分配第一个container来运行ApplicationMaster，若遇到失败则重启运行ApplicationMaster的container。

- **NodeManager**: 运行在每个节点上，管理container，监控其使用情况，并定期向ResourceManager汇报

- **ApplicationMaster**:每个application拥有一个，负责和ResourceManager申请资源；和NodeManager交互来执行/监控task

- 关于container：一组计算资源，如内存、cpu、磁盘、网络等；一个节点多container

### 提交应用

1. client向RM提交应用，请求一个ApplicationMaster实例
2. RM找到一个可以运行container的NodeManager，在该container里启动ApplicationMaster实例
3. ApplicationMaster向RM注册，client即可入通过RM获得ApplicationMaster的详细信息，之后client可直接与applicationMaster交互获得任务进度状态信息
4. ApplicationMaster向RM请求资源
5. Container被成功分配后，ApplicationMaster向NodeManager发送信息启动对应的container
6. Task在container中运行，container把task对应的状态信息发送给ApplicationMaster
7. 当应用执行完成，ApplicationMaster向RM取消注册后关闭

  ### Scheduler

