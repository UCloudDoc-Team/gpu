# 常见问题

## 如何打开pm模式
1. 进入主机，执行一下操作开启

   ```bash
   sudo tar -xvjf nvidia-persistenced-init.tar.bz2
   cd nvidia-persistenced-init/
   sudo bash ./install.sh 
   ```

2. 验证是否开启成功

   ```bash
   nvidia-smi
   ```
   
