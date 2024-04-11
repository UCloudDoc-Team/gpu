# LLaMA-Factory快速部署

## 简介
LLaMA-Factory 是一个涵盖预训练、指令微调到 RLHF 阶段的开源全栈大模型微调框架，具备高效、易用、可扩展的优点，配备有零代码可视化的一站式网页微调界面 LLaMA Board。
同时包含预训练、监督微调、RLHF等多种训练方法，支持 0-1 复现 ChatGPT 训练流程，丰富的中英文参数提示，实时的状态监控和简洁的模型断点管理，支持网页重连和刷新。


## 快速部署
登录UCloud控制台（https://console.ucloud.cn/uhost/uhost/create )，机型选择“GPU型”，“高性价比显卡6”，CPU及GPU颗数等详细配置按需选择。</br>
最低推荐配置：16核CPU 64G内存 1颗GPU。</br>
镜像选择“镜像市场”，镜像名称搜索“LLaMA-Factory”,选择该镜像创建GPU云主机即可。</br>
GPU云主机创建成功之后，登录GPU云主机。</br>
![image](/images/practice/llama-factory01.png)</br>
登录页面如下所示：</br>
![image](/images/practice/llama-factory02.png)</br>

## 操作实践
通过浏览器访问：http://ip:7860 即可，注意ip需要替换为云主机的外网ip，如果无法访问请在控制台检查安全规则是否放行上述端口
### 1.加载装置
![image](/images/practice/llama-factory03.png)</br>
  注：1、2 的模型必须一一对应，2的path为虚机本地模型下载的path。
### 2.操作
![image](/images/practice/llama-factory04.png)</br>
### 3.训练参数
![image](/images/practice/llama-factory05.png)</br>

使用web ui训练后可以点refresh adapters刷新本地Lora权重，从adapter path指定训练好的Lora权重。

选定权重后，训练会从训练好的Lora权重继续训练。

选定权重后，对话会和训练后的Lora模型进行对话。

注：由于webui有路径规则，所以只能识别由webui训练的Lora权重,无法识别命令行训练的权重。
![image](/images/practice/llama-factory06.png)</br>
Quantization bit 需要选择4，否则当前推荐机型的24G显存吃紧。

Prompt template 是和对应模型绑定的，在上面选择model name后会自动带出，不用调整。
![image](/images/practice/llama-factory07.png)</br>
在图中位置选择训练数据集，点击preview dataset可以预览训练数据。

如果需要添加自定义数据集，请参考  /home/ubuntu/LLaMA-Factory/data/README.md
![image](/images/practice/llama-factory08.png)</br>
Learning rate 在 1e-5到5e-4间（不改optimizer的情况）。带有玄学性质，在刷分时可以多试几个学习率。

Epochs 根据loss下降图像调整，小于1万条的数据集可以从epochs=3开始试，如果loss图像尾部没变平缓就往上调。超过6万条文本（1条平均20字以上）的数据集在epochs=1至3之间。

Maximum gradient norm 推荐值在1-10之间。

Max samples 是限定使用数据集的前多少条，一般是全量训练，测试时可以设值为10避免验证时间过长。
![image](/images/practice/llama-factory09.png)</br>
Cutoff length 是训练句子截断长度，句子越长，显存占用越多，如果显存不够可以考虑降低到512甚至256。可以根据微调目标需要的长度进行设置。微调后，模型处理长度大于cut off length的句子的能力会下降。 

Batch size 会影响模型的训练质量，训练速度以及显存。考虑训练质量方面，一般Batch size * Gradient accumulation 的数值在16到64之间，这个乘积是最终的等效batch size。考虑训练速度，batch size在4090的可观测范围内越大越快。考虑显存，batch size =4 是极限，放不下就调低。

句子很长的情况下，如2048，可以batch size=1, gradient accumulation=16。gradient accumulation不影响显存，但是影响等效batch size，进而影响训练质量。
![image](/images/practice/llama-factory10.png)</br>

Save steps 是每多少step存一次模型断点，方便在训练意外断掉的情况继续训练。总step数=epochs*数据集条数/batch size / gradient accumulation，这在训练时有个进度条也会显示。由于每次储存的断点都是模型的全部权重，所以一个断点就是存几十个G。

建议调高至1000以上，否则硬盘容量会很容易不足。每次训练完后，可以到/home/ubuntu/LLaMA-Factory/saves/ChineseLLaMA2-13B-Chat/lora/... 下进行删除。
![image](/images/practice/llama-factory11.png)</br>
可以通过Preview command 得到可以在命令行运行的等效命令，Output dir 是训练结果以及断点的存放路径。设置好参数后可以直接点start进行训练。也可以复制命令在/home/ubuntu/LLaMA-Factory 下，conda的llama_factory环境下运行。




