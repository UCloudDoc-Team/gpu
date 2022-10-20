# AI绘画stable diffusion 实践

## 1.创建一台GPU云主机
创建流程参考[创建第一台云主机](https://docs.ucloud.cn/uhost/newuser/briefguide)
?> 创建GPU云主机时，镜像选择“AI绘画stable diffusion平台”，操作路径：镜像市场——>AI绘画stable diffusion平台,便捷安装stable diffusion,镜像内置环境：CentOS 7.8；</br>
   推荐机型：GPU型云主机 T4/T4S；</br>
   内存请选择32GB及以上，否则模型加载时可能会触发OOM；</br>
   绑定EIP并在外网防火墙放行TCP 8888端口。
   
 ## 2.虚机内部启动jupyter
 
 ```
 #nohup jupyter notebook &
 ```
 目的在于后续可以在web页面浏览生成的图片，也可在web页面直接编写python交互式程序。

?> 若需要开机自启，可以自行通过rc.local或systemctl配置。

## 3. 使用stable diffusion
### 3.1 方式一：使用stable diffusion的sample script
#### 3.1.1 切换conda环境
切换conda环境
```
conda activate ldm
```
#### 3.1.2 执行sample脚本
执行脚本,输入您预想图画的描述，即可得到图片（以下以“a painting of Mads Mikkelsen smoking”为例），生成的图片在 /root/stable-diffusion/outputs/txt2img-samples/目录下。
```
cd stable-diffusion
python scripts/txt2img.py --prompt "a painting of Mads Mikkelsen smoking"
```
#### 3.1.3 使用jupyter页面查看
根据外网ip地址，访问http://EIP:8888
![image](/images/software/keep1.jpg)

输入token（在/root/.jupyter/jupyter_notebook_config.py中查看c.NotebookApp.token的配置，可自行修改。）
![image](/images/software/keep1.jpg)

根据导航点击预览图片
![image](/images/software/keep1.jpg)

### 3.2 方式二：使用stable diffusion的lib
#### 3.2.1 切换到指定目录
cd到指定目录
cd /root/demo
#### 3.2.2 执行python test.py
!> 需确保在ldm的conda环境中运行
```
test.py
from torch import autocast
from diffusers import StableDiffusionPipeline
 
pipe = StableDiffusionPipeline.from_pretrained(
    "./stable-diffusion-v1-4").to("cuda")
 
prompt = "a photo of an astronaut riding a horse on mars"
with autocast("cuda"):
    image = pipe(prompt)["sample"][0] 
     
image.save("astronaut_rides_horse.png")
```
生成图片astronaut_rides_horse.png

#### 3.2.3 使用jupyter页面查看


 
   
   
   
