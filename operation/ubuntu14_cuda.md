

# Ubuntu 14.04 环境配置

## 1. 检查GPU设备识别
```
  $ sudo lspci | grep NVIDIA
  3D controller: NVIDIA Corporation GK210GL [Tesla K80] 表示识别为K80
  3D controller: NVIDIA Corporation GP102GL [Tesla P40] (rev a1) 表示为P40
```

## 2. 获取cuda网络源，并配置：

NVidia官方源地址 http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1404/x86_64/

```
  $ wget http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1404/x86_64/cuda-repo-ubuntu1404_8.0.44-1_amd64.deb
  $ sudo dpkg -i cuda-repo-ubuntu1404_8.0.44-1_amd64.deb
  $ sudo apt-get update
```

## 3. 安装cuda 8.0
```
  $ sudo apt-get install cuda-8.0
  注：在安装前请uname -a  检测当前内核版本，然后确保对应版本的kernel-header包已经安装，否则无法正常编译驱动。
  $ uname -a
  $ Linux X-X-X-X 3.13.0-123-generic #172-Ubuntu SMP Mon
  $ sudo aptitude search 3.13.0-123-generic
  $ p   linux-cloud-tools-3.13.0-123-generic   - Linux kernel version specific cloud tools for version 3.13.0-123                      
  $ p   linux-headers-3.13.0-123-generic      - Linux kernel headers for version 3.13.0 on 64 bit x86 SMP                             
  $ p   linux-headers-3.13.0-123-generic:i386   - Linux kernel headers for version 3.13.0 on 32 bit x86 SMP
  $ sudo apt-get install  linux-headers-3.13.0-123-generic 
```

### 3.1 查看驱动状态
<code>
$ sudo nvidia-smi
</code>
看到如下输出表示GPU驱动正常：

![](ai/gpu/images/operation/nvidia.jpg)

## 4. 测试GPU基本功能（可选）

### 4.1 增加LD path
<code>
  $ export LD_LIBRARY_PATH="/usr/local/cuda-7.5/lib64:/usr/lib64/:$LD_LIBRARY_PATH"
</code>

### 4.2 安装cuda examples
```
  $ cd /usr/local/cuda/bin
  $ sh cuda-install-samples-8.0.sh ~/cuda-test/
  $ cd ~/cuda-test/NVIDIA_CUDA-8.0_Samples
  $ make
  $ ./bin/x86_64/linux/release/deviceQuery 获取设备状态
  $ ./bin/x86_64/linux/release/bandwidthTest 测试设备带宽
  Note: 如果编译过程发现lnvcuvid的错误，可以执行：
  $ find . -type f -execdir sed -i 's/UBUNTU_PKG_NAME = "nvidia-367"/UBUNTU_PKG_NAME = "nvidia-375"/g' '{}' \    
```
其中nvidia-375是当前安装的驱动的版本

## 5. 安装cudnn
选装，注：不同AI框架对cudnn的版本支持不同

### 5.1 下载cudnn软件包
https://developer.nvidia.com/cudnn ，需要注册nvidia账号后才能下载

### 5.2 安装
案例使用cudnn5.1, 因为TensorFlow目前仅支持5.1

ubuntu 可以选择 cuDNN v5.1 Runtime Library for Ubuntu14.04 (Deb)
<code>
  $ sudo dpkg -i libcudnn5_5.1.10-1+cuda8.0_amd64.deb
</code>

## 5. 关闭ubuntu自动更新内核及NVidia Tools
建议操作
<code>
$ sudo vim /etc/apt/apt.conf.d/10periodic
</code>
将 APT::Periodic::Update-Package-Lists "1"; 修改为 APT::Periodic::Update-Package-Lists "0";
以禁止ubuntu自动更新软件包

## FAQ

### 1. nvidia-smi 发现 GPU使用率100%，为什么？

这个问题是系统读取gpu状态信息不准确导致，执行下列命令可更正，让系统读取命令正确。
<code>
    # nvidia-smi -pm 1
</code>

