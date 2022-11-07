# AI绘画stable diffusion 实践

## 1.创建一台GPU云主机
创建流程参考[创建第一台云主机](https://docs.ucloud.cn/uhost/newuser/briefguide)</br>
创建GPU云主机时，镜像选择“AI绘画stable diffusion平台”，操作路径：镜像市场——>AI绘画stable diffusion平台，便捷安装stable diffusion，镜像内置环境：CentOS 7.8。</br> 
推荐机型：GPU型云主机 T4/T4S、V100S、P40。

?> 内存请选择32GB及以上，否则模型加载时可能会触发OOM。</br> 
绑定EIP并在外网防火墙放行TCP 8888端口。
   
 ## 2.虚机内部启动jupyter
 
 ```
 nohup jupyter notebook &
 ```
 目的在于后续可以在web页面浏览生成的图片，也可在web页面直接编写python交互式程序。

?> 若需要开机自启，可以自行通过rc.local或systemctl配置。

## 3. 使用stable diffusion
### 3.1 方式一：使用stable diffusion的sample script
#### 3.1.1 切换conda环境

```
conda activate ldm
```
#### 3.1.2 执行sample脚本
执行脚本，输入您预想图画的描述，即可得到图片（以下以“a painting of Mads Mikkelsen smoking”为例），生成的图片在 /root/stable-diffusion/outputs/txt2img-samples/目录下。
```
cd stable-diffusion
python scripts/txt2img.py --prompt "a painting of Mads Mikkelsen smoking"
```
#### 3.1.3 使用jupyter页面查看
根据外网ip地址，访问http://EIP:8888
![img](/images/practice/jupyter1.png)

输入token（在/root/.jupyter/jupyter_notebook_config.py中查看c.NotebookApp.token的配置，可自行修改。）
![img](/images/practice/jupyter2.png) </br>
![img](/images/practice/jupyter3.png)

根据导航点击预览图片
![img](/images/practice/jupyter5.png)

### 3.2 方式二：使用jupyter nodebook新建ldm环境（推荐）
![img](/images/practice/ldm1.png)


添加如下示例代码后运行（以"A thriving view alongside Pearl of the Orient in Shanghai , by Van Gogh, oil painting trending on artstation HQ"为示例）
```
from torch import autocast
from diffusers import StableDiffusionPipeline
from IPython.display import Image
 
width = 512
height = 512
 
pipe = StableDiffusionPipeline.from_pretrained(
    "/root/demo/stable-diffusion-v1-4").to("cuda")
 
prompt = "A thriving view alongside Pearl of the Orient in Shanghai , by Van Gogh, oil painting trending on artstation HQ"
with autocast("cuda"):
    image = pipe(prompt, height, width)["sample"][0] 
     
image.save("Van_Gogh_Style_Shanghai.png")
 
# show the image in web
Image(filename = 'Van_Gogh_Style_Shanghai.png', width=width, height=height)
```
?> 1. 选中代码分区，点击【运行】，如出现In[* ]，则表示代码运行中，静等出图结果即可；</br>
   2. 如您需要调整画布尺寸，需保证调整后长宽的均为8的整数倍。

生成图片即可立即查看
![img](/images/practice/ldm2.png)

?> 如您想快速尝试，还可直接使用demo/路径下的SD_Demos.ipynb，内置英文版、中文版模型。

 
   
   
   
