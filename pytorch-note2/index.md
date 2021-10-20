# PyTorch学习笔记（二）计算图与自动求梯度


# 1.计算图与动态图

## 1.1计算图

### 1.1.1定义

随着神经网络层数的加深，各种张量间的计算变得越来越复杂，各个计算之间应该并行执行还是顺序执行？底层设备如何避免重复计算减少冗余？如何提高计算效率？我们使用计算图解决这些问题。

计算图是用于描述计算的**有向无环图**，我们都知道图包括节点和边，在计算图中节点代表数据，边代表运算

<img src="https://s1.imagehub.cc/images/2021/10/20/compute_graph9e064e5ba5444736.png" alt="compute_graph9e064e5ba5444736.png" style="zoom: 50%;" />

### 1.1.2张量的属性

在计算图中张量有重要的几个属性

（1） `is_leaf`：叶子节点是直接创建的节点，设置叶子节点的目的是**节省内存**，在反向传播后，非叶子节点是直接被释放掉的

（2）`grad_fn`：记录创建张量时所用的具体方法，主要用于**梯度求导**

## 1.2动态图

根据计算图的构建模式，可将计算图分为动态图和静态图

- 静态图：先搭建图后运算
- 动态图：运算与搭建同时进行（PyTorch）

# 2.自动求梯度

> 参考书籍《动手学深度学习》

在深度学习的任务中，我们经常需要对函数求梯度，PyTorch 提供的 `autograd` 包能够根据输入的前向传播的过程自动构建计算图，执行反向传播求梯度。

## 2.1autograd包

`Tensor` 是这个包的核心类，如果将属性 `requires_grad` 设为 `True` ，将开始追踪在这个张量上的所有操作，可以利用链式法则传播，最后调用 `backward()` 方法完全梯度计算，如果不想要被继续追踪，可以调用 `detach()` 将其从追踪记录中分离出来

> 注意！在 y.backward() 时，如果 y 是标量，则不需要为 backward() 传入任何参数；否则，需要 传入⼀个与 y 同形的 Tensor 

## 2.2方法

（1）PyTorch 自动求梯度机制使用的是 `torch.autograd.backward` 方法，功能是自动求梯度

```python
torch.autograd.backward(tensors, grad_tensors=None, retain_None, create_graph=False)
```

- tensors：表示用于求导的张量，如loss
- retain_graph：表示保存计算图， 由于PyTorch采用了动态图机制，在每一次反向传播结束之后，计算图都会被释放掉。如果我们不想被释放，就要设置这个参数为True
- create_graph：表示创建导数计算图，用于高阶求导。
- grad_tensors：表示多梯度权重。如果有多个loss需要计算梯度的时候，就要设置这些loss的权重比例。

（2）梯度清零：grad在反向传播过程中是累加的，这意味着每一次进行反向传播，梯度都会累加之前的梯度，所以要在反向传播之前需把梯度清零 `zero_gard()`

## 2.3注意

（1）梯度不会自动清零，每一次进行反向传播都会累加之前的梯度，我们在训练神经网络时每进行一次反向传播，都要手动清除梯度

```python
# 清空梯度
optimizer.zero_grad()
# 前向传播
output = model(input)
# 计算loss
batch_loss = loss(output, label)
# 反向传播
batch_loss.backward()
# 更新参数
optimizer.step()
```

（2）依赖于叶子节点的节点，`requires_grad` 默认设为 `True` 

（3）叶子结点不可执行 in-place（原位操作）

- in-place操作， 这个操作就是在原始内存当中去改变这个数据，例如列表的 append 操作即原位操作，**此过程没有新对象产生**
- 反向传播是根据地址去找 w 的值，如果在反向传播前原位操作将 w 值改变了，反向传播变会出错

## 2.4实例

创建一个 `Tensor` 并设置 `requires_grad=True`

```python
import torch
x = torch.ones(2, 2, requires_grad=True)
print(x.grad_fn)

# 输出 None
```

做一次加法运算操作

```python
y = x + 2
print(y.grad<fn)

# 输出 <AddBackward0 object at 0x000001AF717C8B0>
print(x.is_leaf, y.is_leaf) # True False
```

x 是直接创建的没有 `grad_fn` ，而 y 是通过加法操作得到的，像 x 这种直接创建的称为**叶子节点**

```python
z = y * y
out = z.mean()
```

因为 out 是⼀个标量，所以调用 `backward()` 时不需要指定求导变量

```python
out.backward()
print(x.grad)
# 输出
tensor([[1.5000, 1.5000],
        [1.5000, 1.5000]])
```

我们分析一下计算过程与求导结果：
$$
out=\frac{1}{4}\sum_{i=1}^{4}z_{i}=\frac{1}{4}\sum_{i=1}^{4}(x_{i}+2)^{2}
$$

$$
\frac{\partial out}{\partial x_{i}}=\frac{3}{2}=1.5
$$

量都为向量的函数 y = f(x) , 那么 y 关于 x 的梯度就是一个雅可比矩阵，而`torch.autograd` 这个包就是用来计算雅可比矩阵的乘积
























































