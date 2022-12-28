# 抢占式GPU云主机

## 产品简介

抢占式GPU云主机相较于按时付费的GPU云主机具有超低的折扣，旨在为客户在部分场景下提供更加便捷低成本的GPU算力资源。

抢占式GPU云主机的列表价会跟随市场价格和库存余量的情况，发生变动。如后台资源不足，或者客户的出价低于当前列表价，则会邮件通知使用方，将在**30min**之后回收资源。

?> 抢占式GPU云主机目前在逐步灰度，如您有此类需求，可联系客户经理开通测试权限。

## 适用场景
抢占式云主机适用于无状态、支持断点续算的应用场景，例如图像渲染、高性能并行计算、大数据业务等。应用程序的分布度、可扩展性和容错性越高，越适合使用抢占式GPU云主机节省资源成本，提升吞吐量。

抢占式云主机上不宜运行对稳定性要求较高的服务，因为系统资源不足或者竞价失败等因素会导致抢占式云主机被释放，任务运行中断，可能会影响正常业务运行。

## 生命周期
抢占式GPU云主机生命周期如下图所示：

![image](/images/operation/spotflow.png)

?> 抢占式GPU云主机创建之后可以平稳运行一个小时，在这一小时之内不受系统资源不足或者价格调整的影响，均能平稳使用。

   如后台资源不充裕或市场价格大于您的出价，系统将会邮件通知客户，抢占式GPU云主机将在半个小时之后被释放，请提前做好数据备份。
   
   您可以在创建GPU云主机时，同步开启快照服务。如仍需继续使用，可以通过快照回复GPU云主机。


## 计费说明

目前仅云主机支持抢占式，即CPU、内存和GPU卡可以享受超低折扣，云主机关联的EIP、磁盘仍按照原价进行售卖；

如抢占式GPU云主机资源欠费，会立即进入回收列表，邮件通知客户立即续费，5min之后如还未正常续费，则资源将被关机回收；

抢占式GPU云主机不支持做计费方式变更，不支持改配升级；

抢占式GPU云主机不受客户销售折扣影响，不可享受叠加折扣优惠。









