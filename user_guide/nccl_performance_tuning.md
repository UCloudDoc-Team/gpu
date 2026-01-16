# NCCL性能调优

NVIDIA 集合通信库 (NCCL) 可实现针对 NVIDIA GPU 和网络进行性能优化的多 GPU 和多节点通信基元。

本文适用于`高性价显卡6`与`高性价显卡7`系列

## 编译环境准备

以 Ubuntu24.04 为例。

```sh
sudo apt update
sudo apt install -y devscripts debhelper fakeroot build-essential
```

## NVIDIA驱动安装

如`nvidia-smi`命令有输出，可跳过此步骤。

[官网下载NVIDIA驱动](https://www.nvidia.com/download/index.aspx?lang=cn)

以`NVIDIA-Linux-x86_64-570.211.01.run`为例

```sh
# 1. 添加执行权限
chmod +x NVIDIA-Linux-x86_64-570.211.01.run
# 2. 安装 (执行后一直enter即可)
sudo ./NVIDIA-Linux-x86_64-570.211.01.run
# 3. 确认正常输出
nvidia-smi
```

## CUDA安装

如`nvcc -V`命令有输出, 可跳过此步骤。

[官网下载CUDA](https://developer.nvidia.com/cuda-toolkit-archive)

以`cuda_12.8.1_570.124.06_linux.run`为例

```sh
# 1. 添加执行权限
chmod +x cuda_12.8.1_570.124.06_linux.run
# 2. 安装 (输入accept后取消Driver的勾选然后Install)
sudo ./cuda_12.8.1_570.124.06_linux.run
# 3. 追加环境变量
sudo sh -c "cat >> /etc/profile <<'EOF'
export CUDA_HOME=/usr/local/cuda
export PATH=\$PATH:/usr/local/cuda/bin
export LD_LIBRARY_PATH=/usr/local/cuda/lib64:\$LD_LIBRARY_PATH
EOF"
# 4. 使能环境变量
source /etc/profile
# 5. 确认正常输出
nvcc -V
```

## NCCL-Tests安装

```sh
# 1. 下载/编译/安装nccl
git clone https://github.com/NVIDIA/nccl.git
cd nccl
git checkout v2.26.5-1
make pkg.debian.build CUDA_HOME=/usr/local/cuda -j$(nproc)
cd build/pkg/deb/
sudo apt install ./*.deb
# 2. 下载/编译nccl-tests
cd
git clone https://github.com/NVIDIA/nccl-tests.git
cd nccl-tests
make CUDA_HOME=/usr/local/cuda -j$(nproc)
# 3. 运行nccl-tests
cd build
NCCL_NTHREADS=512 NCCL_MIN_NCHANNELS=8 NCCL_MAX_NCHANNELS=8 NCCL_BUFFSIZE=524288 ./all_reduce_perf -b 128M -e 1G -f 2 -g 8
```

| 参数                 | 含义                                        |
| -------------------- | ------------------------------------------- |
| NCCL_NTHREADS=512    | 每个 NCCL 通信 channel 使用 512 个 GPU 线程 |
| NCCL_MIN_NCHANNELS=8 | 限制 NCCL 至少使用 8 个通信 channel         |
| NCCL_MAX_NCHANNELS=8 | 限制 NCCL 最多使用 8 个通信 channel         |
| NCCL_BUFFSIZE=524288 | 每个 channel 使用 512 KB 通信缓冲区         |
| -b 128M              | 起始消息大小为 128 MB                       |
| -e 1G                | 最大消息大小为 1 GB                         |
| -f 2                 | 消息大小按 2 倍递增                         |
| -g 8                 | 使用 8 张 GPU 参与 AllReduce 通信           |

> UCloud已对GPU云主机底层虚拟化进行升级，可通过lspci命令确认是否存在QEMU PCIe Expander bridge确认
> 部分存量运行的云主机，可以在控制台上关机/开机一次, 将自动为您完胜升级
> 若不进行升级，测试时需要指定NCCL_TOPO_FILE为/var/run/nvidia-topologyd/virtualTopology.xml (运行docker时也请透传此文件)
