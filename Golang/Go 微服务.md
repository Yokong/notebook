![](https://github.com/Yokong/notebook/blob/master/Golang/images/bilibili%20Go%20%E5%BE%AE%E6%9C%8D%E5%8A%A1.png)
## 互联网架构演进之路

单体架构->垂直架构->SOA架构(面向服务)->微服务架构

## 微服务架构介绍

* 根据业务模块划分服务各类
* 每个服务可独立部署且互相隔离
* 通过轻量的API调用服务
* 服务需要保证高可用

## 微服务优势

* 松耦合
* 独立发布
* 快速迭代
* 故障隔离

## 微服务挑战

* DevOps要求
* 系统复杂性
* 数据一致性
* 测试复杂性

## Go 微服务架构最佳实践

构架: go-micro  
协议: protobuf  
服务发现: consul/etcd  
配置管理: etcd  
服务监控: prometheus, grafana  

## 微服务架构6大核心点

* API Gateway
* 进程间通信
* 服务注册发现
* 事件驱动的数据管理
* 微服务部署策略
* 微服务化改造
