# ChatGLM-6B模型

## 简介
ChatGLM-6B是清华大学知识工程和数据挖掘小组发布的一个开源的对话机器人。根据官方介绍，这是一个千亿参数规模的中英文语言模型。并且对中文做了优化。本次开源的版本是其60亿参数的小规模版本，约60亿参数。该模型的基础模型是GLM（ GLM: General Language Model Pretraining with Autoregressive Blank Infilling ），是一个千亿基座模型。

## 快速部署
登录UCloud控制台（https://console.ucloud.cn/uhost/uhost/create )，机型选择“GPU型”，“V100S”，CPU及GPU颗数等详细配置按需选择。</br>
最低推荐配置：10核CPU 32G内存 1颗V100S。</br>
镜像选择“镜像市场”，镜像名称搜索“ChatGLM-6B”,选择该镜像创建GPU云主机即可。</br>
GPU云主机创建成功之后，登录GPU云主机。</br>
![image](/images/practice/GLM04.png)</br>
登录页面如下所示：</br>
![image](/images/practice/GLM01.png)</br>
我们预装的镜像提供如下信息：</br>
1.模型本地地址</br>
2.运行本地聊天机器人</br>
3.运行网页聊天机器人</br>
4.使用ADGEN数据集微调模型</br>
以网页聊天机器人为例，执行的结果如下：</br>
![image](/images/practice/GLM02.png)
![image](/images/practice/GLM03.png)





