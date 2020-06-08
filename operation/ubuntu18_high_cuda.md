

# 高内核Ubuntu 18.04 环境配置

高内核Ubuntu 18.04镜像的内核为UCloud优化的4.19.0-4.ucloud

## 1. 检查GPU设备识别

```
  $ sudo lspci | grep NVIDIA
  3D controller: NVIDIA Corporation GK210GL [Tesla K80] 表示识别为K80
  3D controller: NVIDIA Corporation GP102GL [Tesla P40] (rev a1) 表示为P40
```

## 2. 屏蔽开源驱动nouveau

编辑如下文件：
```
sudo vim /etc/modprobe.d/blacklist-nouveau.conf
```
写入下列内容：
```
blacklist nouveau
blacklist lbm-nouveau
options nouveau modeset=0
alias nouveau off
alias lbm-nouveau off
```
更新并重启：
```
sudo update-initramfs -u
sudo reboot
sudo apt-get install build-essential pkg-config
```
此版本 linux-headers已默认安装。


## 3. 安装nvidia驱动

### 3.1 下载
到nvidia官网下载合适的驱动（目前版本418.126.02），地址https://www.nvidia.com/Download/index.aspx?lang=en-us

也可从UFile下载，速度更快 http://gpu.cn-bj.ufileos.com/NVIDIA-Linux-x86_64-418.126.02.run

### 3.2 安装
```
sudo chmod +x NVIDIA-Linux-x86_64-418.126.02.run
sudo ./NVIDIA-Linux-x86_64-418.126.02.run
```

### 3.3 查看驱动状态
<code>
$ sudo nvidia-smi
</code>
看到如下输出表示GPU驱动正常：

![](ai/gpu/images/operation/nvidia-smi.png)

## 4. 安装cuda库

### 4.1 网络安装

```
sudo wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/cuda-ubuntu1804.pin

sudo mv cuda-ubuntu1804.pin /etc/apt/preferences.d/cuda-repository-pin-600

sudo apt-key adv --fetch-keys http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/7fa2af80.pub

sudo add-apt-repository "deb http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/ /"

sudo apt-get update

sudo apt-get -y install cuda
```

### 4.2 本地安装
```
wget http://developer.download.nvidia.com/compute/cuda/10.2/Prod/local_installers/cuda_10.2.89_440.33.01_linux.run
sudo sh cuda_10.2.89_440.33.01_linux.run
```

## FAQ

### 1. nvidia-smi 发现 GPU使用率100%，为什么？

这个问题是系统读取gpu状态信息不准确导致，执行下列命令可更正，让系统读取命令正确。
<code>
    #sudo nvidia-smi -pm 1
</code>

### 2. 除自行安装外，是否有其它获得驱动镜像的方法？

可提交工单，或联系工作人员，获得UCloud制作的包含GPU驱动和Cuda环境的镜像，节省人工安装的时间。
