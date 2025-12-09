# NVIDIA驱动和CUDA安装
## NVIDIA
1. 控制台创建云主机，选择想要安装驱动的基础镜像版本，创建虚机
2. [访问NVIDIA官网获取下载地址](https://www.nvidia.com/download/index.aspx?lang=cn)
   - 产品家族选择具体机型
   - CUDA ToolKit选择驱动支持的CUDA版本，没有选择则是默认版本
3. 选择完成后，点击搜索→下载，复制链接地址（下图以A100为例：）
     ![image](/images/downloadnv1.png)</br>
     ![image](/images/downloadnv2.png)</br>
     ![image](/images/downloadnv3.png)</br>
4. 进入GPU云主机
   - 执行命令下载驱动(wget后面是复制的链接地址)：`wget {上一步复制的链接地址}`
   - 检查gcc、make软件库是否安装，及安装gcc 和 make
   ```sh
   ## which make 检测make是否安装, 安装命令 # sudo apt-get install make
   ## gcc --version  检测gcc是否安装, 安装命令 # sudo apt-get install gcc
   ```  
5. 开始安装：执行`sh NVIDIA-xxxxxxx.run `，即开始安装驱动，注：遇到权限问题命令前添加sudo即可
6. 验证nvidia驱动：执行 `nvidia-smi`

## CUDA
1. [官网下载CUDA](https://developer.nvidia.com/cuda-toolkit-archive)   
   选择对应系统和CUDA版本（执行 `nvidia-smi` 可查看驱动适配的最高cuda版本，小于等于Nvidia驱动的cuda版本）
2. wget下载到虚机本地，执行 `sudo sh cuda_xxxxxxx_linux.run` 进行安装
3. 配置环境变量，添加软链接
   ```sh
   ## 添加环境变量：
   sudo vim /etc/profile  //编辑文件，在末尾添加,保存退出:
   export CUDA_HOME=/usr/local/cuda
   export PATH=$PATH:/usr/local/cuda/bin
   export LD_LIBRARY_PATH=/usr/local/cuda/lib64:$LD_LIBRARY_PATH
   ## 添加软链接：
   sudo ln -s /usr/local/cuda-{cuda版本号} /usr/local/cuda （修改版本号即可，eg：cuda-10.1）
   ## 重启
   sudo reboot
   ```
4. 验证cuda环境是否配置完成：`nvcc -V`
