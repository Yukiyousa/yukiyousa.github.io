# PyTorch学习笔记（三）数据读取机制与图像预处理模块


# 1.数据读取机制

读取数据的基本流程是：

1. 将存储图片的路径和标签信息转为list，该list可以通过index选取一个元素对应一个样本
2. 通过`getitem`函数，读取数据和标签，并返回数据和标签

## 1.1Dataset

### 1.1.1Dataset类

PyTorch通过Dataset类读取数据，Dataset类作为所有的datasets的基类存在，所有的dataset都要继承它，并且必须复写`__getitem__()`这个类方法。

```python
def __getitem__(self, index):
	raise NotImplementedError
def __len__(self):
	raise NotImplementedError
```

`__getitem__()`接收一个 index，然后返回图片数据和标签，这个 index 通常指的是一个 list 的 index，这个 list 的每个元素就包含了图片数据的路径和标签信息。

### 1.1.2构建Dataset子类

我们构建Dataset子类——MyDataset类：

```python
class MyDataset(Dataset):
	def __init__(self, x, transform=None):
        self.data_info = x # data_info存储所有图片路径和标签，在DataLoader中通过index读取样本
 		self.transform = transform
 	def __getitem__(self, index):
 		path_img, label = self.x[index]
 		img = Image.open(path_img).convert('RGB') # 0~255
 		if self.transform is not None:
 			img = self.transform(img) # 在这里做transform，转为tensor等等
 			return img, label
 	def __len__(self):
 		return len(self.imgs)
```

在MyDataset中，主要获取图片的索引以及定义如何通过索引读取图片及其标签，但要触发MyDataset去读取图片及其标签是在数据加载器DataLoder中

## 1.2DataLoader

`torch.utils.data.DataLoader()`: 构建可迭代的数据装载器, 我们在训练的时候，每一个for循环，每一次迭代，就是从DataLoader中获取一个batch_size大小的数据的。

```python
DataLoader(dataset, batch_size=1, shuffle=False, sampler=None)
```

DataLoader的常用参数：

- dataset: Dataset类， 决定数据从哪读取以及如何读取
- bathsize: 批大小
- num_works: 是否多进程读取机制
- shuffle: 每个epoch是否乱序
- drop_last: 当样本数不能被batch_size整除时， 是否舍弃最后一批数据

# 2.图像预处理transforms

在实际应用过程中，我们会在数据进入模型之前进行一些预处理，例如数据中心化(仅 减均值)，数据标准化(减均值，再除以标准差)，随机裁剪，旋转一定角度，镜像等一系列 操作。transforms 是常用的图像预处理方法

transforms.Compose方法是将一系列的transforms方法进行有序的组合包装，具体实现的时候，依次的用包装的方法对图像进行操作。

## 2.1剪裁

- transforms.CenterCrop(size)：中心裁剪，size是所需裁剪的尺寸
- transforms.RandomCrop：随机裁剪
- transforms.RandomResizedCrop ：随机长宽比裁剪
- transforms.FiveCrop：上下左右中心裁剪 
- transforms.TenCrop：上下左右中心裁剪后翻转

## 2.2翻转和旋转

- transforms.RandomHorizontalFlip(p=0.5)：依概率 p 水平翻转
- transforms.RandomVerticalFlip(p=0.5)：依概率 p 垂直翻转
- transforms.RandomRotation(degrees, resample=False, expand=False, center=None)：随机旋转，degrees表示旋转角度 ， resample表示重采样方法， expand表示是否扩大图片，以保持原图信息。

## 2.3图片变换

- transforms.Resize：重新设定尺寸
- **transforms.ToTenser：将图像转为 tensor，并归一化至[0-1]**
- transforms.Pad(padding, fill=0, padding_mode='constant'): 对图片边缘进行填充
- transforms.Grayscale：转灰度图
- transforms.Normalize：标准化
- transforms.ToPILImage：将图像转换为PILImage

## 2.4选择操作

- transforms.RandomChoice(): 从一系列transforms方法中随机选一个
- transforms.RandomApply(): 依据概率执行一组transforms操作
- transforms.RandomOrder(): 对一组transforms操作打乱顺序









































