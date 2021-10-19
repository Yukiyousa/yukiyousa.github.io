# Pytorch学习笔记（一）张量的定义与操作


# 1.Tensor的创建

## 1.1Tensor的简介

（1）张量是一个多维数组，是标量、向量、矩阵的高维扩展，在Pytorch中，`torch.Tensor`是存储和变换数据的主要工具，它提供了GPU计算的自动求梯度等更多功能

（2）Tensor与Variable：在0.4.0版本后均并入Tensor，Variable有下面五种属性

- data: 被包装的Tensor
- grad: data的梯度
- grad_fn: fn表示function的意思，记录我么创建的创建张量时用到的方法
- requires_grad: 指示是否需要梯度， 有的不需要梯度
- is_leaf: 指示是否是叶子节点（张量）

（3）Tensor属性

- dtype：张量的数据类型，最常用`float32`和`int64`
- shape：张量的形状
- device：张量所在的设备，CPU或GPU

## 1.2Tensor的创建

（1）直接创建张量`torch.Tensor()`

```python
t = torch.Tensor(data,devicr='cuda')
```

（2）通过numpy数组生成`torch.from_numpy()`

```python
arr = np.array([[1,2,3],[4,5,6]])
t = torch.from_numpy(arr)
```

（3）依据数值创建

```python
torch.zeros()  # 创建全零张量
torch.ones()
torch.full()  # t = torch.full((3,3), 10)
torch.arange(start, end, steps)  # 创建等差的1维张量,数值区间[start, end)，step表示步长
torch.linspace(start, end, steps)  # 创建均分的1维张量,数值区间[start, end]，steps指的是列表的长度
torch.eye()  # 创建对角矩阵，默认方阵
```

（4）依据概率创建张量`torch.noraml(mean, std, size, out=None)`

```python
torch.normal()  # mean是均值，std是标准差
torch.randn()  # 生成标准正态分布
torch.rand()  # 生成均匀分布
torch.randperm(n)  # 生成从0 - n-1的随机排列, n是张量的长度
```

# 2.Tensor的操作

## 2.1基本操作

（1）拼接

```
torch,cat(tensors, dim, out=None)  # 将张量按维度dim进行拼接
torch.stack(tensors, dim, out=None)  # 在新创建的维度dim上进行拼接 
```

（2）切分

```python
torch.chunk(input, chunks, dim=0)  # 将张量按维度dim进行平均切分，返回值是张量列表
torch.split(tensor, split_size_or_sections, dim=0)  # 可以指定切分的长度， split_size_or_sections为int时表示每一份的长度
```

## 2.2数学操作

TODO











































