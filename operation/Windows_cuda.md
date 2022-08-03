# Windows 环境配置

## Windows 驱动安装

1.登录GPU云主机；

2.访问[NVIDIA驱动下载](https://www.nvidia.com/Download/Find.aspx) 官网；

3.根据实例操作系统及GPU规格，选择操作系统和安装包，本文以T4为例，如下图所示：
![image](https://github.com/UCloudDoc-Team/gpu/blob/master/images/operation/driver.png)

4.打开下载驱动程序所在的文件夹，双击安装文件开始安装，按照界面上的提示安装驱动程序并根据需要重启实例。
安装完成后，如需验证 GPU 是否正常工作，请查看设备管理器。

## 常见报错

1. windows安装驱动报错缺少wlanapi.dll
解决方式：打开【服务器管理器】窗口，选择【管理】菜单中的【添加角色和功能】选项，启动【添加角色和功能向导】，也可以直接点击【仪表板】中【快速启动】的【添加角色和功能】链接来启动。
![image](https://github.com/UCloudDoc-Team/gpu/blob/master/images/operation/wlanapi1.png)
![image](https://github.com/UCloudDoc-Team/gpu/blob/master/images/operation/wlanapi2.png)

!> 该步骤需要重启系统后才能生效。 




