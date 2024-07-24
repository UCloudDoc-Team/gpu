# NCCL性能调优

NVIDIA 集合通信库(NCCL) 可实现针对NVIDIA GPU 和网络进行性能优化的多GPU 和多节点通信基元。   

## GPU云主机内NCCL测试步骤
> 确认环境：需在8卡高性价比显卡6/高性价比显卡6pro/A800云主机下
> 确认依赖：vn驱动，cuda，gcc依赖：版本>=8，nccl依赖包，topo文件

1. 下载NCCL-Test
```sh
# 下载并编译nccl-test
git clone https://github.com/NVIDIA/nccl-tests.git
cd nccl-tests
make CUDA_HOME=/usr/local/cuda -j
#   执行命令
~/nccl-tests/build$ ./all_reduce_perf -b 8 -e 128M -f 2 -g 8
```

2. 指定拓扑文件

```sh
# 执行命令，导出topo文件
NCCL_TOPO_DUMP_FILE=path/file ./all_reduce_perf -b 8 -e 128M -f 2 -g 1 -t 8
```

## GPU云主机NCCL TOPO文件透传至容器

当您在8卡高性价比显卡6/高性价比显卡6pro/A800等类型的GPU云主机内自行使用docker时，可根据下列步骤完成topo透传：   

1. 确认GPU云主机内是否存在该文件/var/run/nvidia-topologyd/virtualTopology.xml
  - 若存在执行第2步   
  - 若不存在请咨询技术支持，将提供您该文件，拷贝文件保存至GPU node的 /var/run/nvidia-topologyd/virtualTopology.xml后执行第2步   
2.  在GPU云主机内执行下列操作
     
```sh
docker run -it -e NVIDIA_VISIBLE_DEVICES=all -v /var/run/nvidia-topologyd/virtualTopology.xml:/var/run/nvidia-topologyd/virtualTopology.xml  ubuntu /bin/bash
```

## NCCL性能优化配置

```sh
NCCL_MIN_NCHANNELS=32   //nccl可以使用的最小通道数
NCCL_MAX_NCHANNELS=32   //nccl可以使用的最大通道数，增加通道数也会增加nccl使用的cuda块数，这可能有助于提高性能，2.5以上nccl版本最大值为32
NCCL_NTHREADS=256       //设置每个cuda块的cuda线程数，gpu时钟频率较低并且想要增加线程数量，可调整此参数；新一代gpu，默认值是512
NCCL_BUFFSIZE=2097152   //nccl在gpu对之间传递数据时使用的缓冲区的大小，默认4194304（4MB），值是整数，以字节为单位
```



