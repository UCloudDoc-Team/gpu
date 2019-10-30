

# CentOS 7 环境配置

## 1. 检查GPU设备识别

<code>
  $ yum install pciutils

  $ sudo lspci | grep NVIDIA
  3D controller: NVIDIA Corporation GK210GL [Tesla K80] 表示识别为K80
  3D controller: NVIDIA Corporation Device 1b38 (rev a1) 表示为P40
</code>

## 2. 获取cuda网络源，并配置

NVidia官方源地址http://developer.download.nvidia.com/compute/cuda/repos/rhel7/x86_64/
<code>
  $ wget http://developer.download.nvidia.com/compute/cuda/repos/rhel7/x86_64/cuda-repo-rhel7-8.0.61-1.x86_64.rpm

  $ rpm -Uvh cuda-repo-rhel7-8.0.61-1.x86_64.rpm
</code>
注：安装nvidia驱动需要kernel-devel包，安装方法如下：
<code>
  $ wget http://vault.centos.org/7.0.1406/updates/x86_64/Packages/kernel-devel-3.10.0-123.4.4.el7.x86_64.rpm

  $ wget http://vault.centos.org/7.0.1406/updates/x86_64/Packages/kernel-headers-3.10.0-123.4.4.el7.x86_64.rpm
  $ rpm -Uvh kernel-devel-3.10.0-123.4.4.el7.x86_64.rpm
  $ rpm -Uvh kernel-headers-3.10.0-123.4.4.el7.x86_64.rpm
</code>
## 3. 安装cuda 8.0

  $ yum install cuda-8-0

### 3.1 查看驱动状态
  $ sudo nvidia-smi
看到如下输出表示GPU驱动正常：

{{ai:gpu:operation:nvidia.jpg|}}

## 4. 测试GPU基本功能（可选）

### 4.1 增加LD path
  $ export LD_LIBRARY_PATH="/usr/local/cuda-7.5/lib64:/usr/lib64/:$LD_LIBRARY_PATH"
### 4.2 安装cuda examples
  $ cd /usr/local/cuda/bin
  $ sh cuda-install-samples-8.0.sh ~/cuda-test/
  $ cd ~/cuda-test/NVIDIA_CUDA-8.0_Samples
  $ make
  $ ./bin/x86_64/linux/release/deviceQuery 获取设备状态
  $ ./bin/x86_64/linux/release/bandwidthTest 测试设备带宽

Note: 如果编译过程发现lnvcuvid的错误，可以执行：
  $ find . -type f -execdir sed -i 's/UBUNTU_PKG_NAME = "nvidia-367"/UBUNTU_PKG_NAME = "nvidia-375"/g' '{}' \  

其中nvidia-375是当前安装的驱动的版本

## 5. 安装cudnn

选装，注：不同AI框架对cudnn的版本支持不同

### 5.1 下载cudnn软件包
https://developer.nvidia.com/cudnn，需要注册nvidia账号后才能下载。

注意：CentOS 下载 cuDNN v5.1 Library for Linux
         
### 5.2 安装
案例使用cudnn5.1, 因为TensorFlow目前仅支持5.1
  $ tar -zxf cudnn-8.0-linux-x64-v5.1.tgz 

解压的路径可以自由选择，一般是/usr/lib下面，这边假设为<CUDNN_INSTALL_PATH>
  $ export LD_LIBRARY_PATH=:$LD_LIBRARY_PATH

## FAQ

### 1. nvidia-smi 发现 GPU使用率100%，为什么？

这个问题是系统读取gpu状态信息不准确导致，执行下列命令可更正，让系统读取命令正确。

\# nvidia-smi -pm 1