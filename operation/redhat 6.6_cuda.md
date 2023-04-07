# redhat 6.6环境配置

## 1.检查GPU设备识别
```
  $ yum install pciutils
  $ sudo lspci | grep NVIDIA
  3D controller: NVIDIA Corporation Device 1df6 (rev a1) 表示识别为V100S
```
?> 运行"yum install pciutils"提示"This system is not registered with an entitlement server. You can use subscription-manager to register."
    请运行一下命令启动redhat账号登录：
    ```
    $ subscription-manager register
    Registering to: subscription.rhsm.redhat.com:443/subscription
    Username: *****
    Password: 
    The system has been registered with ID: ******************************
    The registered system name is: 10-13-47-75
    ```
    按照提示输入 Red Hat 帐户的用户名和密码。
    确认系统已成功注册，并启用订阅:
    ```
    subscription-manager list --consumed
    ```
    运行以下命令以更新系统：
    ```
    yum update
    ```
 ## 2.下载GPU驱动
 ```
 wget https://cn.download.nvidia.com/tesla/460.106.00/NVIDIA-Linux-x86_64-460.106.00.run
 ```
 ?> 驱动版本可根据业务需求从nvidia官方链接下载，https://www.nvidia.cn/Download/index.aspx?lang=cn。
 
 ## 3.
 





