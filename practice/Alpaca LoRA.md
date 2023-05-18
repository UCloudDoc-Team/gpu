# Alpaca-LoRA7B模型
## 简介
Alpaca LoRA是使用Lora(Low-rank Adaptation)技术在Meta的LLaMA 7B模型上微调，只需要训练很小一部分参数就可以获得媲美 Standford Alpaca 模型的效果。可以认为是ChatGPT轻量级的开源版本。

## 快速部署
登录UCloud控制台（https://console.ucloud.cn/uhost/uhost/create )，机型选择“GPU型”，“V100S”，CPU及GPU颗数等详细配置按需选择。
最低推荐配置：10核CPU 64G内存 1颗V100S。</br>
镜像选择“镜像市场”，镜像名称搜索“Alpaca-LoRA7B”,选择该镜像创建GPU云主机即可。</br>
GPU云主机创建成功之后，登录GPU云主机。</br>
![image](/images/practice/LoRA7B01.png)</br>
![image](/images/practice/LoRA7B02.png)</br>
登录页面如下所示：</br>
![image](/images/practice/LoRA7B03.png)</br>
我们预装的镜像提供如下信息：</br>
1.微调</br>
2.推理</br>
以推理为例，展示页面如下：
![image](/images/practice/LoRA7B04.png)</br>
