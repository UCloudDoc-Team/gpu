# “Ziya”模型快速部署

## 简介
“Ziya”模型是由IDEA开发的一款领先的自然语言处理模型。基于LLaMa的130亿参数的大规模预训练模型，具备翻译、编程、文本分类、信息抽取、摘要、文案生成、常识问答和数学计算等能力。目前“Ziya”模型已完成大规模预训练、多任务有监督微调和人类反馈学习三阶段的训练过程。

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

