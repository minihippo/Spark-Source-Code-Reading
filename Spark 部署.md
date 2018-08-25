# Spark 部署

### Yarn

部署模式：

- cluster集群模式：driver运行在yarn的AM进程内。client定期轮询AM获取状态更新并在控制台显示信息
- client客户端模式：driver运行在client进程中，AM仅用于请求资源



### Standalone

