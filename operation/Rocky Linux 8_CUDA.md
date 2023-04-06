# Rocky Linux 8环境配置

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

## 3.将 Nvidia驱动程序添加到我们的软件包管理器列表中
```
sudo dnf config-manager --add-repo https://developer.download.nvidia.com/compute/cuda/repos/rhel8/x86_64/cuda-rhel8.repo
```
## 4.安装kernel和headers


