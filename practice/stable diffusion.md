# AI绘画stable diffusion 实践

## 创建一台GPU云主机
创建流程参考[创建第一台云主机](https://docs.ucloud.cn/uhost/newuser/briefguide)
?> 创建GPU云主机时，镜像选择“AI绘画stable diffusion平台”，操作路径：镜像市场——>AI绘画stable diffusion平台,便捷安装stable diffusion,镜像内置环境：CentOS 7.8；</br>
   推荐机型：GPU型云主机 T4/T4S；</br>
   内存请选择32GB及以上，否则模型加载时可能会触发OOM；</br>
   绑定EIP并在外网防火墙放行TCP 8888端口。
   
 ## 虚机内部启动jupyter
 
 '''
 #nohup jupyter notebook &
 '''
 
 
   
   
   
