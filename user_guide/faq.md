# 常见问题

## 如何打开pm模式
1. 进入主机，执行以下操作

   ```bash
   cd /usr/share/doc/NVIDIA_GLX-1.0/samples
   sudo tar -xvjf nvidia-persistenced-init.tar.bz2
   sudo bash ./nvidia-persistenced-init/install.sh 
   ```

2. 验证是否开启成功

   ```bash
   nvidia-smi
   ```
   ![image](/images/nvidia-smi.png)</br>
