---
title: AI大模型知识整理
date: 2024-10-29 09:55:12
tags: AI 大模型 Transformer PyTorch 知识库 RAG 微调 多模态 蒸馏 剪枝 量化 推理
---

### AI大模型知识整理

#### 神经网络架构
    CNN 卷积神经网络 使用卷积层来提取特征，通常包括池化层和全连接层。
    RNN 递归神经网络 具有循环连接，能够处理序列数据。每个时间步的输出依赖于前一个时间步的状态。
    Transformer 基于自注意力机制，使用编码器-解码器架构。没有循环结构，能够并行处理输入数据。

 #### PyTorch
    开源的深度学习框架，可以调用不同神经网络架构实现大模型
 
 #### 知识库
    私有化数据，提供了可供检索的信息资源，可以是文档、图片等

#### RAG（Retrieval-Augmented Generation）检索增强生成
    利用知识库中的信息来增强生成模型的能力，以生成更准确的回答。通过embedding（存入和查询数据库）将知识库数据存储、检索、回答结合大模型返回结果

### 微调（Fine-Tuning）
    基于预训练模型（GPT、LLaMA、qwen 等）进行简单训练，学习特定数据

### 多模态        
    通常指的大模型都是基于文本生成文本，要想通过文本生成图片、图片生成文本等非文本数据转化称为多模态

### 蒸馏
    模型迁移（模型学习模型），降低学习成本
    
### 剪枝  
    减少模型参数，降低学习成本
    
### 量化      
    减小精度，降低学习成本

### 推理    
    部署后运行模型执行问答
    工具有 ollama、ModelScope，集成多种大模型，可以方便的下载、运行，训练、部署等
    
### 参考        
[大白话讲讲现在的 AI 和一堆乱七八糟的东西都是啥-闪客](https://mp.weixin.qq.com/s/LGsLCor1NeVq5lDqVx8uQw)  
[https://modelscope.cn/](https://modelscope.cn/)  
[https://ollama.com/](https://ollama.com/)  
[https://bailian.console.aliyun.com/](https://bailian.console.aliyun.com/)  
