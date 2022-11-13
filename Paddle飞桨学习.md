# Paddle 百度飞桨

对于前端的学习有点厌烦，便又去试试百度飞桨，也就是人工智能、深度学习之类的，为什么要加一个又字，是因为很早之前就尝试过，却放弃深入了解，这次厌烦中本想刷刷知乎、B 站，但是大量的重复评论和配音视频，更加烦躁之余，突然惊醒，当年围棋一战后看似退潮的人工智能，其实已经悄无声息的改变一切，既然厌烦前端无聊的学习，那就拾起来之前的放弃的，再搞一搞。

## 先说说我的认识

怎么定义人工智能，我的理解是去模拟人类的思考方式，在接触飞桨的课程后对于当代人工智能技术有一个细节的认识，也就是人工智能技术是哪些，而不是一个概念

首先是深度学习，有一个顺序要搞清楚，那就是人工智能》机器学习》深度学习，可以说深度学习改变了人工智能的发展。深度学习是如何理解我们人类很容易做出判断的事情，例如一个图片，一段文字，这里面转换是如何做到的，那就是模拟人类的意识基础——生物神经元，感知器，一个有输入、处理、分类和输出组成的神经元，一个输入刺激，经过轴突，将信号再传递给下一个神经元，也就是另一个感知器，通过感知器的不断组合、链接，当复杂度足够高时，就可以模拟出一个近似人的智能。这里，借用零基础实践深度学习[机器学习和深度学习综述](https://www.paddlepaddle.org.cn/tutorials/projectdetail/3520300)一句话，通俗的讲，深度学习的模型可以视为是输入到输出的映射函数，如图像到高级语义（美女）的映射，足够深的神经网络理论上可以拟合任何复杂的函数。因此神经网络非常适合学习样本数据的内在规律和表示层次，对文字、图像和语音任务有很好的适用性。这几个领域的任务是人工智能的基础模块，因此深度学习被称为实现人工智能的基础也就不足为奇了

这也是我对于当下人工智能技术一个宏观的理解，也大概直到，数据——模型——训练——验证——调整——再验证——达标，应该是人工智能技术一个基本的逻辑链

## 今天的新认知

今天，我重新拾起来百度飞桨的教程，不过这次没有看视频，而是直接上手敲代码，尝试做一个模型出来，还好，百度飞桨有一个[十分钟快速上手飞桨](https://www.paddlepaddle.org.cn/documentation/docs/zh/guides/beginner/quick_start_cn.html)，这次更是直接体验到实际的人工智能编码是怎么回事，具体细节先放一边，先说下我的感受

1. 基本懵逼这个状态贯穿全部，每行代码在教程的讲解下我都知道怎么回事，但是基本不了解有什么含义。
2. 深度学习任务一般的流程：数据集的定义与加载、模型组网、模型训练与评估、模型推理
3. 百度飞桨这些年确实积攒了很多行业实践，应该快要到达一个全行业通用的人工智能研发、应用的规模和能力，其实这要这几届大学生陆续进入各个行业将百度飞桨带入到行业内，基本上市场也就成了，这可是未来中国最稀缺的一个资源，人的再生产。

## 我的收获

1. 具体的过程还是看百度教程全面，我所有的认知也仅限于知道了深度学习的一般开发路径。
2. 百度技术能力已经很强悍了，有很多工具可以摸索。
3. BML Codelab，也就是 Jupyter，这个 python 工具第一次使用，我好喜欢啊，边编辑边输出看结果，实在是 6 的飞起。

## 手写数字识别

相当于编程界的 hello world，很典型的一个深度学习项目

先看看十分钟版本

大致基本上就用 paddle 内置的数据集、模型网路和模型训练模式

```python
import paddle
import numpy as np
from paddle.vision.transforms import Normalize

transform = Normalize(mean=[127.5], std=[127.5], data_format='CHW')
# 下载数据集并初始化 DataSet
train_dataset = paddle.vision.datasets.MNIST(mode='train', transform=transform)
test_dataset = paddle.vision.datasets.MNIST(mode='test', transform=transform)

# 模型组网并初始化网络
lenet = paddle.vision.models.LeNet(num_classes=10)
model = paddle.Model(lenet)

# 模型训练的配置准备，准备损失函数，优化器和评价指标
model.prepare(paddle.optimizer.Adam(parameters=model.parameters()),
              paddle.nn.CrossEntropyLoss(),
              paddle.metric.Accuracy())

# 模型训练
model.fit(train_dataset, epochs=5, batch_size=64, verbose=1)
# 模型评估
model.evaluate(test_dataset, batch_size=64, verbose=1)

# 保存模型
model.save('./output/mnist')
# 加载模型
model.load('output/mnist')

# 从测试集中取出一张图片
img, label = test_dataset[0]
# 将图片shape从1*28*28变为1*1*28*28，增加一个batch维度，以匹配模型输入格式要求
img_batch = np.expand_dims(img.astype('float32'), axis=0)

# 执行推理并打印结果，此处predict_batch返回的是一个list，取出其中数据获得预测结果
out = model.predict_batch(img_batch)[0]
pred_label = out.argmax()
print('true label: {}, pred label: {}'.format(label[0], pred_label))
# 可视化图片
from matplotlib import pyplot as plt
plt.imshow(img[0])
```

而另外一个教程则复杂一点，全面一点

首先现有一个极简的手写数字识别模型，但是在模型网络上不同，一个用的是经典网络模型，一个是自定义的网络模型

现在继续的是数据集

按照这里的说法，其实整个深度学习技术是被分为两部分，数据集与模型训练，模型网络设计，数据集与训练模型由应用开发者主导，而模型网络设计则是大学科研机构解决吗？

无法确定训练出来的模型可以做出什么，这个黑盒模式下，商业运作反而很谨慎，只愿意将这个开发过程优化便利，但是不能指出一个确定的路线

有搞头

ok

到此截至，从网络结构以下我已经看不懂了，但得记下这些步骤

1. 数据处理
2. 网络结构，也就是模型网络
3. 损失函数
4. 优化算法
5. 资源配置
6. 训练调试与优化
7. 恢复训练
8. 动静转化部署

而在文档中，有个简化点的步骤

1. 数据集定义与加载
2. 数据预处理
3. 模型组网
4. 模型训练、评估与推理
5. 模型保存与载入

再次尝试下去了解 paddle 整体的技术栈
