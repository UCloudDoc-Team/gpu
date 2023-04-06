# Rocky Linux 8.5环境配置

## 1.检查GPU设备识别
```
  $ yum install pciutils
  $ sudo lspci | grep NVIDIA
  3D controller: NVIDIA Corporation GV100GL [Tesla V100S PCIe 32GB] (rev a1) 表示识别为V100S
```

## 2.获取并安装Epel
```
sudo dnf install epel-release
```

## 3.将 Nvidia驱动程序添加到软件包管理器列表中
```
sudo dnf config-manager --add-repo https://developer.download.nvidia.com/compute/cuda/repos/rhel8/x86_64/cuda-rhel8.repo
```
## 4.安装kernel和headers
```
sudo dnf install kernel-devel-$(uname -r) kernel-headers-$(uname -r)
```
## 5.安装NVIDIA驱动和配置
```
sudo dnf install nvidia-driver nvidia-settings
```
## 6.安装CUDA
```
sudo dnf install cuda-driver
```
## 7.重启系统并验证
重启操作系统之后，通过"nvidia-smi"命令来查看显卡是否正常工作。
```
[root@10-13-47-75 ~]# nvidia-smi
Thu Apr  6 17:10:52 2023       
+---------------------------------------------------------------------------------------+
| NVIDIA-SMI 530.30.02              Driver Version: 530.30.02    CUDA Version: 12.1     |
|-----------------------------------------+----------------------+----------------------+
| GPU  Name                  Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf            Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|                                         |                      |               MIG M. |
|=========================================+======================+======================|
|   0  Tesla V100S-PCIE-32GB           Off| 00000000:00:03.0 Off |                    0 |
| N/A   26C    P0               35W / 250W|      0MiB / 32768MiB |      0%      Default |
|                                         |                      |                  N/A |
+-----------------------------------------+----------------------+----------------------+
                                                                                         
+---------------------------------------------------------------------------------------+
| Processes:                                                                            |
|  GPU   GI   CI        PID   Type   Process name                            GPU Memory |
|        ID   ID                                                             Usage      |
|=======================================================================================|
|  No running processes found                                                           |
+---------------------------------------------------------------------------------------+
```


