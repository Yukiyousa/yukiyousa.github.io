# PyTorch学习笔记（四）模型搭建与权值初始化


# 1.模型构建步骤

## 1.1两大模块

- 构建子模块：卷积层、池化层、全连接层，自己建立的模型（继承nn.Module）的`__init__()`方法
- 拼接子模块：构建了子模块，将子模块按一定的顺序排序，逻辑拼接得到最终的网络模型，在模型的`forward()`方法中

## 1.2具体步骤

- 首先，必须继承 **nn.Module** 这个类，要让 PyTorch 知道这个类是一个 Module。
- 其次，在**init(self)**中设置好需要的“组件"(如 conv、pooling、Linear、BatchNorm 等)。 
- 最后，在 **forward(self, x)**中用定义好的“组件”进行组装，就像搭积木把网络结构搭建 出来，这样一个模型就定义好了。

# 2.nn.Module类

torch.nn是神经网络模块，有以下子模块

- nn.Parameter：张量子类，表示可学习参数、如weight，bias
- nn.Module：所有网络基类，管理网络属性
- nn.functional：函数具体实现，如卷积、池化、激活函数等
- nn.init：参数初始化方法

nn.Module是所有网络层的基类，管理有关网络的属性。

## 2.1nn.Parameter

PyTorch一般将参数用`nn.Parameter`来表示，并且用`nn.Module`来管理其结构下的所有参数。

## 2.2nn.functional

`nn.functional`(一般引入后改名为F)有各种功能组件的函数实现，为便于对参数管理，一般通过nn.Module转换为类的实现形式，封装在nn模块下：

- 激活函数变成(nn.ReLu, nn.Sigmoid, nn.Tanh, nn.Softmax)
- 模型层(nn.Linear, nn.Conv2d, nn.MaxPool2d, nn.Embedding)
- 损失函数(nn.BCELoss, nn.MSELoss, nn.CrossEntorpyLoss)

## 2.3nn.Module

nn.Module是所有网络层的基类，管理有关网络的属性，在其中有8个重要的属性

![nn.png](https://s1.imagehub.cc/images/2021/10/20/nn.png)

# 3.nn.Sequential

torch.nn.Sequential 其实就是 Sequential 容器，该容器将一系列操作**按先后顺序**给包起来，方便重复使用

```python
class LeNet(nn.Module):
	def __init__(self, classes):
		super(LeNetSequential, self).__init__()
	    self.features = nn.Sequential(
	        nn.Conv2d(3, 6, 5),
	        nn.ReLU(),
	        nn.MaxPool2d(kernel_size=2, stride=2),
	        nn.Conv2d(6, 16, 5),
	        nn.ReLU(),
	        nn.MaxPool2d(kernel_size=2, stride=2),)
	
	    self.classifier = nn.Sequential(
	        nn.Linear(16*5*5, 120),
	        nn.ReLU(),
	        nn.Linear(120, 84),
	        nn.ReLU(),
	        nn.Linear(84, classes),)
	
	def forward(self, x):
	    x = self.features(x)
	    x = x.view(x.size()[0], -1)
	    x = self.classifier(x)
	    return x
```

**模型定义就是先继承，再构建组件，最后组装**。基本组件可从 torch.nn 中获取，同时为了方便重复使用组件，可以使用 Sequential 容器将一系列组件包起来，最后在 forward() 函数中将这些组件组装成模型。

# 4.nn网络层

## 4.1卷积层

二位卷积层网络参数，n表示输入大小，f表示卷积核大小，p表示padding，s表示步长

```python
nn.Conv2d(in_channels, out_channels, kernel_size, stride, padding)
```

- 无padding: n - f / s + 1
- 有padding: n + 2p -f / s + 1

## 4.2池化层

池化层是可以帮助我们剔除一些冗余像素

```python
nn.MaxPool2d(kernel_size, stride, padding)
nn.AvgPool2d(kernel_size, stride, padding)
nn.MaxUnpool2d(kernel_size, stride, padding)
```

## 4.3线性层

线性层又称为全连接层，其每个神经元与上一层所有神经元相连实现对前一层的**线性组合，线性变换**

```python
nn.Linear(in_features, out_features, bias=True)
```

# 5.权值初始化

在网络模型搭建完成之后，对网络中的权重进行合适的初始化是非常重要的一个步骤， 初始化好的情况下初始化在模型的最优解附近，模型训练速度提升，反之模型需要多次训练

## 5.1权值初始化流程

- 第一步，先设定什么层用什么初始化方法，初始化方法在 torch.nn.init 中给出
- 第二步，实例化一个模型之后，执行该函数，即可完成初始化

## 5.2常用初始化方法

### 5.2.1Xavier初始化

（1）均匀分布：初始化方法服从均匀分布U(-a, a)

```python
torch.nn.init.xavier_uniform_(tensor, gain=1)
```

（2）正态分布：初始化方法服从正太分布

```python
torch.nn.init.xavier_normal_(tensor, gain=1)
```

（3）使用方法

```python
def initialize(self):
    for m in self.modules():
        if isinstance(m, nn.Linear):
            # Xavier初始化权重
            tanh_gain = nn.init.calculate_gain('tanh')
            nn.init.xavier_uniform_(m.weight.data, gain=tanh_gain)
```

### 5.2.2kaiming初始化

```python
torch.nn.init.kaiming_uniform_(tensor, a=0, mode='fan_in', nonlinearity='leaky_relu')
torch.nn.init.kaiming_normal_(tensor, a=0, mode='fan_in', nonlinearity='leaky_relu')
```






























