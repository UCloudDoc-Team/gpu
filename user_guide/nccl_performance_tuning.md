# NCCL性能调优

NVIDIA 集合通信库(NCCL) 可实现针对NVIDIA GPU 和网络进行性能优化的多GPU 和多节点通信基元。   

## GPU云主机内NCCL参数配置

## GPU云主机NCCL TOPO文件透传至容器

当您在8卡高性价比显卡6/高性价比显卡6pro/A800等类型的GPU云主机内自行使用docker时，可根据下列步骤完成topo透传：   

1. 确认GPU云主机内是否存在该文件/var/run/nvidia-topologyd/virtualTopology.xml
  - 若存在执行第2步   
  - 若不存在请咨询技术支持，将提供您该文件，拷贝文件保存至GPU node的 /var/run/nvidia-topologyd/virtualTopology.xml后执行第2步   
2.  在GPU云主机内执行下列操作   
```sh
docker run -it -e NVIDIA_VISIBLE_DEVICES=all -v /var/run/nvidia-topologyd/virtualTopology.xml:/var/run/nvidia-topologyd/virtualTopology.xml  ubuntu /bin/bash
```

## NCCL测试指导
