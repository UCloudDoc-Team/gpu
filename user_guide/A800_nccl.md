# A800 NCCL多机通信
## gpu环境准备
### 1. 驱动和cuda
```
用行业镜像创建云主机默认带gpu驱动和cuda，
如果想自行安装gpu驱动和cuda，请参考网址：https://developer.nvidia.com/cuda-toolkit-archive
```
  
### 2. 加载nvidia_peermem内核mod
```
cuda11.4以上版本自带此内核mod，低于此版本则需要额外安装nv_peer_mem，建议加入开机自启动
sudo modprobe nvidia_peermem
```

### 3. 安装nvidia-fabricmanager
```
# ubuntu
# version为nvidia driver版本
version=535.54.03
main_version=$(echo $version | awk -F '.' '{print $1}')
sudo apt update
sudo apt -y install nvidia-fabricmanager-${main_version}=${version}-*
sudo systemctl start nvidia-fabricmanager
sudo systemctl status nvidia-fabricmanager
sudo systemctl enable nvidia-fabricmanager
```


## A800 虚拟网卡配置
#### A800云主机创建结束，默认有一个eth0，此网络一般用于管理面流量转发。对于多机GPU通信场景，需要每台云主机创建4张虚拟网卡来进行数据面流量转发。以下是网卡配置的最佳实践
### 1. 创建子网
```
在私有网络VPC界面创建新的VPC，网段为192.168.0.0/16，初始子网subnet-eth1网段为192.168.1.0/24
```
![image](/images/a800nccl1.png)</br>
```
然后切换到子网tab栏，选中刚才创建的VPC，
新增子网subnet-eth2，网段为192.168.2.0/24
```
![image](/images/a800nccl2.png)</br>
```
新增子网subnet-eth3，网段为192.168.3.0/24
新增子网subnet-eth4，网段为192.168.4.0/24
```

### 2. 虚拟网卡创建和挂载
```
4个子网创建完成后，切换到虚拟网卡tab栏，每台A800云主机都需要4张虚拟网卡，4张虚拟网卡分别对应4个子网
第1张虚拟网卡对应子网subnet-eth1
第2张虚拟网卡对应子网subnet-eth2
第3张虚拟网卡对应子网subnet-eth3
第4张虚拟网卡对应子网subnet-eth4
虚拟网卡创建完成后，按照相同的顺序将虚拟网卡挂载到每台云主机上
例如挂载到每台云主机的第一张网卡都是从子网subnet-eth1申请的虚拟网卡
```
![image](/images/a800nccl3.png)</br>


### 3. 虚拟网卡初始化
```
虚拟网卡配置完成，需要在云主机内部初始化网络配置，每次开机都需执行，建议加入开机自启动
# ubuntu
mtu=4200
devs="eth1 eth2 eth3 eth4"
for dev in $devs
do
    sudo ip link set $dev mtu $mtu
    sudo ip link set $dev up 
    sudo dhclient $dev
done
devs="mlx5_0 mlx5_1 mlx5_2 mlx5_3"
for dev in $devs
do
    sudo cma_roce_tos -d $dev -t 184
    sudo cma_roce_mode -d $dev -m 2 
    sudo echo 184 > /sys/class/infiniband/$dev/tc/1/traffic_class
done
sudo pkill dhclient
sudo modprobe rdma_cm
```

## nccl多机通信验证
### 1. 下载并编译nccl-test
```
git clone https://github.com/NVIDIA/nccl-tests.git
cd nccl-tests
make MPI=1 MPI_HOME=/usr/mpi/gcc/openmpi-4.1.5a1 CUDA_HOME=/usr/local/cuda -j
```
### 2. nccl多机测试命令
```
mpirun --allow-run-as-root --oversubscribe -np {gpu卡数量} --bind-to numa -H {内网ip地址} -mca plm_rsh_args "-p 22 -q -o StrictHostKeyChecking=no" -mca coll_hcoll_enable 0 \
-mca pml ob1 -mca btl_tcp_if_include eth0 -mca btl ^openib -mca btl_openib_if_include mlx5_0:1,mlx5_1:1,mlx5_2:1,mlx5_3:1 -mca btl_openib_cpc_include rdmacm -mca btl_openib_rroce_enable 1 -x NCCL_IB_DISABLE=0 \
-x NCCL_SOCKET_IFNAME=eth0 -x NCCL_IB_GID_INDEX=3 -x NCCL_IB_TC=184 -x NCCL_IB_TIMEOUT=23 -x NCCL_IB_RETRY_CNT=7 -x NCCL_IB_PCI_RELAXED_ORDERING=1 \
-x NCCL_IB_HCA=mlx5_0,mlx5_1,mlx5_2,mlx5_3 -x CUDA_VISIBLE_DEVICES=0,1,2,3,4,5,6,7 -x NCCL_TOPO_FILE={nccl_topo_file文件} -x NCCL_NET_GDR_LEVEL=1 \
-x CUDA_DEVICE_ORDER=PCI_BUS_ID -x NCCL_ALGO=Ring -x LD_LIBRARY_PATH -x PATH {all_reduce_perf文件} -b 8 -e 8G -f 2 -g 1
# gpu卡数量：云主机机个数*8（每台云主机8张gpu卡）
# 内网ip地址：云主机eth0对应的ip地址，多个ip之间之间英文，隔开
# nccl_topo_file文件: 指定nccl_topo.xml文件绝对路径
# all_reduce_perf文件：指定all_reduce_perf文件绝对路径，例如：$HOME/nccl-tests/build/all_reduce_perf
```
