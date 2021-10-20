# PyTorch学习笔记（五）损失函数与优化器


# 1.损失函数

损失函数是衡量模型输出与真实情况差异的标准，我们所说的优化即优化网络权值使得损失函数变得更小，一般而言在机器学习中往往有三个概念损失函数、代价函数、目标函数

- Loss Function：计算一个样本的一个差异 Loss = f(y^, y)
- Cost Function：计算整个训练集Loss的一个平均值
- Objective Function：在机器学习模型训练中，这是最终的目标，过拟合和欠拟合进行权衡

## 1.1交叉熵损失CrossEntropyLoss

nn.CrossEntropyLoss是nn.LogSortmax()与nn.NLLLoss()结合，进行交叉熵计算，交叉熵=信息熵+相对熵

## 1.2其他常用损失函数

TripletMarginLoss：三元组损失函数，目的是让Postive元和Anchor元之间的距离尽可能的小，Postive元和Negative元之间的距离尽可能的大。

# 2.优化器

在机器学习中可分为五大步骤：数据 -> 模型 -> 损失 -> 优化器 -> 迭代训练。我们在前向传播的过程中，得到了模型输出与真实标签的差异称为损失，有了损失我们通过反向传播更新梯度，接下来的过程中，我们需要优化器进行梯度下降，优化器根据梯度更新参数，使得损失不断降低。

## 2.1什么是优化器

优化器是**管理**并**更新**模型中可学习参数的值， 使得模型输出更接近真实标签。

## 2.2优化器基类Optimizer

### 2.2.1基本属性

Optimizer优化器的基本属性：

- defaults: 优化器超参数，里面会存储一些学习了， momentum的值，衰减系数等
- state: 参数的缓存， 如momentum的缓存（使用前几次梯度进行平均）
- param_groups: 管理的参数组， 是个列表，每一个元素是一个字典，在字典中有key，key里面的值才是我们真正的参数（**这个很重要， 进行参数管理**）
- _step_count: 记录更新次数， 学习率调整中使用， 如迭代100次之后更新学习率的时候，就得记录这里的100.

### 2.2.2基本方法

优化器的基本方法

- zero_grad()：清空所管理参数的梯度，PyTorch一个特性是**张量梯度不自动清零**
- step()：执行一步更新，其中可传入参数 closure（一个闭包）。
- add_param_group()：添加参数组，对优化器的参数进行分组管理，对不同组的参数可以设置不同的超参数
- state_dict()：获取优化器当前状态信息**字典**，key 是各层参数名，value 就是参数
- load_state_dict()：加载状态信息字典，将 state_dict 中的参数加载到当前网络，常用于 finetune

```python
for input, target in dataset: 
	def closure(): 
		optimizer.zero_grad() 
		output = model(input) 
		loss = loss_fn(output, target) 
		loss.backward() 
		return loss
    optimizer.step(closure)
```

## 2.3常用优化器

- optim.SGD: 随机梯度下降法
- optim.Adagrad: 自适应学习率梯度下降法
- optim.RMSprop: Adagrad的改进
- optim.Adadelta: Adagrad的改进
- optim.Adam: RMSprop结合Momentum
- optim.Adamax: Adam增加学习率上限
- optim.SparseAdam: 稀疏版的Adam
- optim.ASGD: 随机平均梯度下降
- optim.Rprop: 弹性反向传播
- optim.LBFGS: BFGS的改进

## 2.4学习率调整策略

在优化器当中有很多超参数，例如学习率，动量系数等，这里面最重要的一个参数就是学习率。它直接控制了参数更新步伐的大小，整个训练当中，学习率也不是一成不变的，也可以调整和变化。 

### 2.4.1为什么要调整学习率

在训练模型时，一般一开始学习率比较大，这样可以以较快速度达到最优点附近，再将学习率调低，缓慢接近收敛值

### 2.4.2学习率调整策略

- StepLR ：等间隔调整学习率
- MultiStepLR：按给定间隔调整学习率
- ExponentialLR：按指数衰减调整学习率
- CosineAnnealingLR：余弦周期调整学习率
- ReduceLRonPlateau：监控指标， 当指标不再变化则调整， **这个非常实用**。可以监控loss或者准确率，当不在变化的时候，我们再去调整。
- LambdaLR：自定义调整策略

### 2.4.3分类

PyTorch提供了六种学习率调整策略，可分为三大类

- 有序调整：等间隔下降(Step)， 按需设定下降间隔(MultiStep)，指数下降(Exponential)和 CosineAnnealing

- 自适应调整：依训练状况伺机调整，这就是 ReduceLROnPlateau 方法

- 自定义调整：Lambda 方法提供的调整策略十分灵活，我们可以为不同的层设定不同的学习率调整方法，这在 fine-tune （微调）中十分有用

  











































