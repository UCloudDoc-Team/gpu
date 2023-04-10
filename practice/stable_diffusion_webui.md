# AI绘画Web UI使用指南

## 1.创建一台GPU云主机
创建流程参考[创建第一台云主机](https://docs.ucloud.cn/uhost/newuser/briefguide)</br>
创建GPU云主机时，镜像选择“AI绘画stable diffusion平台”，操作路径：镜像市场——>AI绘图 Web UI，便捷安装AI绘画Web UI页面，镜像内置环境：CentOS 7.8。</br> 
推荐机型：GPU型云主机 T4/T4S、V100S、P40。

?> 内存请选择32GB及以上，否则模型加载时可能会触发OOM。</br> 
绑定EIP并在外网防火墙放行TCP 7860端口。

## 2.登录GPU云主机
登录GPU云主机，按照系统首页提示，输入如下命令，启动stable diffusion Web UI。
```
su ucloud
cd /home/ucloud/stable-diffusion-webui
export GRADIO_SERVER_NAME=0.0.0.0
export GRADIO_SERVER_PORT=7860
python ./launch.py --xformers 
```
 ![img](/images/practice/SDwebui_01.png)

## 3.登录Web UI页面进行绘图
登录hhtp://eip:7860即可访问stable diffusion Web UI页面。

![img](/images/practice/SDwebui_02.png)
