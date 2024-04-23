# Llama3-8B-Instruct-Chinese 快速部署

## 简介 
Llama3 由 Meta 是在15万亿tokens数据集上训练的，是 Llama2的7倍，包括4倍的代码数据。其中预训练数据集中还有5%的非英文数据集，总共支持的语言高达30种，在做其他语言能力对齐方面也会更有优势。Llama 3 Instruct 更是针对对话应用进行了优化，结合了超过 1000 万的人工标注数据，通过监督式微调（SFT）、拒绝采样、邻近策略优化（PPO）和直接策略优化（DPO）进行训练。本文提及的模型即为基于中文语料指令微调之后的模型（Llama3-8B-Instruct-Chinese），在中文表现上有相对不错的效果。

## 快速部署
登录UCloud控制台（https://console.ucloud.cn/uhost/uhost/create )，机型选择“GPU型”，“高性价比显卡6”，CPU及GPU颗数等详细配置按需选择。</br>
最低推荐配置：16核CPU 32G内存 1颗高性价比显卡6 GPU。</br>
镜像选择“镜像市场”，镜像名称搜索“Llama3”,选择该镜像创建GPU云主机即可。</br>
GPU云主机创建成功之后，登录GPU云主机。</br>
![image](/images/practice/Llama301.png)</br>
登录页面如下所示：</br>
![image](/images/practice/Llama302.PNG)</br>
web操作页面如下所示：</br>
![image](/images/practice/Llama303.PNG)</br>
