# NCCL性能调优

NVIDIA 集合通信库(NCCL) 可实现针对NVIDIA GPU 和网络进行性能优化的多GPU 和多节点通信基元。   

## GPU云主机内NCCL测试步骤
> 确认环境：需在8卡高性价比显卡6/高性价比显卡6pro/A800云主机下   
> 确认依赖：nvidia驱动，cuda，gcc依赖：版本>=8，NCCL依赖包，topo文件

### 依赖不满足
> 若依赖满足，直接跳至[本文NCCL-Test部分]("NCCL-Test")   

这部分为基础环境搭建指导，以Ubuntu系统为例
#### 基础环境搭建
```sh
#  gcc 、g++， make 环境；gcc依赖：版本>=8
/etc/yum.repos.d/CentOS-7-all.repo增加配置：
[sclo]
baseurl=http://yum.service.ucloud.cn/custom/7/sclo/$basearch/
enabled=1
 
apt-get clean all
apt-get makecache
 
apt-get install devtoolset-8-gcc.x86_64 devtoolset-8-gcc-c++.x86_64 devtoolset-8-gcc-gdb-plugin.x86_64 devtoolset-8-gcc-gfortran.x86_64 devtoolset-8-gcc-plugin-devel.x86_64 --skip-broken
scl enable devtoolset-8 bash  # 重启云主机 需要重新加载环境
```

#### NVIDIA驱动和CUDA安装
##### NVIDIA
1. 控制台创建云主机，选择想要安装驱动的基础镜像版本，创建虚机
2. [访问NVIDIA官网获取下载地址](https://www.nvidia.com/download/index.aspx?lang=cn)
   - 产品家族选择具体机型
   - CUDA ToolKit选择驱动支持的CUDA版本，没有选择则是默认版本
3. 选择完成后，点击搜索→下载，复制链接地址
4. 进入GPU云主机
   - 执行命令下载驱动(wget后面是复制的链接地址)：`wget {上一步复制的链接地址}`
   - 检查gcc、make软件库是否安装，及安装gcc 和 make
   ```sh
   # which make 检测make是否安装, 安装命令 # sudo apt-get install make
   # gcc --version  检测gcc是否安装, 安装命令 # sudo apt-get install gcc
   ```  
5. 开始安装：执行`sh NVIDIA-xxxxxxx.run `，即开始安装驱动，注：遇到权限问题命令前添加sudo即可
6. 验证nvidia驱动：执行 ‘vidia-smi’

##### CUDA
1. [官网下载CUDA](https://developer.nvidia.com/cuda-downloads)   
   选择对应系统和CUDA版本（执行 `nvidia-smi` 可查看驱动适配的最高cuda版本，小于等于Nvidia驱动的cuda版本）
2. wget下载到虚机本地，执行 `sudo sh cuda_xxxxxxx_linux.run` 进行安装
3. 配置环境变量，添加软链接
   ```sh
   # 添加环境变量：
   sudo vim /etc/profile  //编辑文件，在末尾添加,保存退出:
   export CUDA_HOME=/usr/local/cuda
   export PATH=$PATH:/usr/local/cuda/bin
   export LD_LIBRARY_PATH=/usr/local/cuda/lib64:$LD_LIBRARY_PATH
   # 添加软链接：
   sudo ln -s /usr/local/cuda-10.1 /usr/local/cuda （修改版本号即可，eg：10.1）
   # 重启
   reboot
   ```
4. 验证cuda环境是否配置完成：`nvcc -V`
#### NCCL环境准备
```sh
# ubuntu 18.04
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/cuda-keyring_1.0-1_all.deb
# ubuntu 20.04
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2004/x86_64/cuda-keyring_1.0-1_all.deb
# ubuntu 22.04
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2204/x86_64/cuda-keyring_1.0-1_all.deb
sudo dpkg -i cuda-keyring_1.0-1_all.deb
sudo apt update
# 下面软件包要和cuda版本匹配（具体版本可查询官方信息（举例ubuntu22.4）：https://developer.download.nvidia.cn/compute/cuda/repos/ubuntu2204/x86_64/）
sudo apt install libnccl2=2.18.3-1+cuda12.2
sudo apt install libnccl-dev=2.18.3-1+cuda12.2
```

### NCCL-Test
#### 1. 下载NCCL-Test
```sh
# 下载并编译nccl-test
git clone https://github.com/NVIDIA/nccl-tests.git
cd nccl-tests
make CUDA_HOME=/usr/local/cuda -j
#   执行命令
~/nccl-tests/build$ ./all_reduce_perf -b 8 -e 128M -f 2 -g 8
```

#### 2. 指定拓扑文件
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



