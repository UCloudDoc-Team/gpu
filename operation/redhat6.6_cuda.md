# Redhat 6.6环境配置

## 1.检查GPU设备识别
```
  $ yum install pciutils
  $ sudo lspci | grep NVIDIA
  3D controller: NVIDIA Corporation Device 1df6 (rev a1) 表示识别为V100S
```

运行"yum install pciutils"提示"This system is not registered with an entitlement server. You can use subscription-manager to register."</br>
请运行以下命令启动Redhat账号登录：</br>
    ```
    $ subscription-manager register
    ```
    
按照提示输入 Redhat 帐户的用户名和密码。</br>
确认系统已成功注册，并启用订阅:</br>
    ```
    subscription-manager list --consumed
    ```
    </br>
    运行以下命令以更新系统：</br>
    ```
    yum update
    ```
    
 ## 2.下载GPU驱动
 ```
 wget https://cn.download.nvidia.com/tesla/460.106.00/NVIDIA-Linux-x86_64-460.106.00.run
 ```
 ?> 驱动版本可根据业务需求从nvidia官方链接下载，https://www.nvidia.cn/Download/index.aspx?lang=cn。
 
 ## 3.禁用nouveau
 因部分linux系统安装的nouveau驱动与nvidia驱动有冲突，因此需先禁用。输入"lsmod  |grep nouveau",如果有返回，则需禁用，禁用方式如下：
 ```
# lsmod | grep nouveau
nouveau              1514531  0 
ttm                    89568  1 nouveau
drm_kms_helper        127731  1 nouveau
drm                   355270  3 nouveau,ttm,drm_kms_helper
i2c_algo_bit            5903  1 nouveau
i2c_core               29164  5 i2c_piix4,nouveau,drm_kms_helper,drm,i2c_algo_bit
mxm_wmi                 1967  1 nouveau
video                  21686  1 nouveau
wmi                     6287  2 nouveau,mxm_wmi
```
 
 ```
 # tail -1 /etc/modprobe.d/blacklist.conf 
   blacklist nouveau
 ```
 ## 4.安装驱动
 ```
 # sudo sh <driver_installer>.run --kernel-source-path=/usr/src/kernels/<kernel_version>
 ```
 其中 <driver_installer> 是驱动程序的安装程序文件名，<kernel_version> 是当前系统检查搭到的内核版本号。
 检查当前正在运行的内核版本，可以通过以下命令来查看：
 ```
 uname -r
 ```
 
 ## 5.验证GPU卡是否正常工作
 使用nvidia-smi来验证，如果能正常显示出卡型，即可正常使用。
 ```
# nvidia-smi
Fri Apr  7 15:02:14 2023       
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 460.106.00   Driver Version: 460.106.00   CUDA Version: 11.2     |
|-------------------------------+----------------------+----------------------+
| GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|                               |                      |               MIG M. |
|===============================+======================+======================|
|   0  Tesla V100S-PCI...  Off  | 00000000:00:03.0 Off |                    0 |
| N/A   26C    P0    35W / 250W |      0MiB / 32510MiB |      0%      Default |
|                               |                      |                  N/A |
+-------------------------------+----------------------+----------------------+
                                                                               
+-----------------------------------------------------------------------------+
| Processes:                                                                  |
|  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
|        ID   ID                                                   Usage      |
|=============================================================================|
|  No running processes found                                                 |
````
 
 


 
 
  
 
 





