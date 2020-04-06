---
layout: post
title: "Numpy 基础知识"
date: 2020-04-05 
description: "Numpy 基础知识"
tag: numpy
---

NumPy的主要对象是同构多维数组。它是一个元素表（通常是数字），所有类型都相同，由非负整数元组索引。在NumPy维度中称为轴。

例如，3D空间中的点的坐标`[1, 2, 1]`具有一个轴。该轴有3个元素，所以我们说它的长度为3.在下面所示的例子中，数组有2个轴。第一轴的长度为2，第二轴的长度为3。

```python
[[ 1., 0., 0.],
 [ 0., 1., 2.]]
```

NumPy的数组类被调用`ndarray`。它也被别名所知 `array`。请注意，`numpy.array`这与标准Python库类不同`array.array`，后者只处理一维数组并提供较少的功能。`ndarray`对象更重要的属性是：

- **ndarray.ndim** - 数组的轴（维度）的个数。在Python世界中，维度的数量被称为rank。
- **ndarray.shape** - 数组的维度。这是一个整数的元组，表示每个维度中数组的大小。对于有 *n* 行和 *m* 列的矩阵，`shape` 将是 `(n,m)`。因此，`shape` 元组的长度就是rank或维度的个数 `ndim`。
- **ndarray.size** - 数组元素的总数。这等于 `shape` 的元素的乘积。
- **ndarray.dtype** - 一个描述数组中元素类型的对象。可以使用标准的Python类型创建或指定dtype。另外NumPy提供它自己的类型。例如numpy.int32、numpy.int16和numpy.float64。
- **ndarray.itemsize** - 数组中每个元素的字节大小。例如，元素为 `float64` 类型的数组的 `itemsize` 为8（=64/8），而 `complex32` 类型的数组的 `itemsize` 为4（=32/8）。它等于 `ndarray.dtype.itemsize` 。
- **ndarray.data** - 该缓冲区包含数组的实际元素。通常，我们不需要使用此属性，因为我们将使用索引访问数组中的元素。

### 一个例子

```python
>>> import numpy as np
>>> a = np.arange(15).reshape(3, 5)
>>> a
array([[ 0,  1,  2,  3,  4],
       [ 5,  6,  7,  8,  9],
       [10, 11, 12, 13, 14]])
>>> a.shape
(3, 5)
>>> a.ndim
2
>>> a.dtype.name
'int64'
>>> a.itemsize
8
>>> a.size
15
>>> type(a)
<type 'numpy.ndarray'>
>>> b = np.array([6, 7, 8])
>>> b
array([6, 7, 8])
>>> type(b)
<type 'numpy.ndarray'>
```



### 数组创建

有几种方法可以创建数组。

例如，你可以使用array函数从常规Python列表或元组中创建数组。得到的数组的类型是从Python列表中元素的类型推导出来的。

```python
>>> import numpy as np
>>> a = np.array([2,3,4])
>>> a
array([2, 3, 4])
>>> a.dtype
dtype('int64')
>>> b = np.array([1.2, 3.5, 5.1])
>>> b.dtype
dtype('float64')
```

也可以在创建时显式指定数组的类型：

```python
>>> c = np.array( [ [1,2], [3,4] ], dtype=complex )
>>> c
array([[ 1.+0.j,  2.+0.j],
       [ 3.+0.j,  4.+0.j]])
```

通常，数组的元素最初是未知的，但它的大小是已知的。因此，NumPy提供了几个函数来创建具有初始占位符内容的数组。这就减少了数组增长的必要，因为数组增长的操作花费很大。

函数`zeros`创建一个由0组成的数组，函数 `ones`创建一个完整的数组，函数`empty` 创建一个数组，其初始内容是随机的，取决于内存的状态。默认情况下，创建的数组的dtype是 `float64` 类型的。

```python
>>> np.zeros( (3,4) )
array([[ 0.,  0.,  0.,  0.],
       [ 0.,  0.,  0.,  0.],
       [ 0.,  0.,  0.,  0.]])
>>> np.ones( (2,3,4), dtype=np.int16 )                # dtype can also be specified
array([[[ 1, 1, 1, 1],
        [ 1, 1, 1, 1],
        [ 1, 1, 1, 1]],
       [[ 1, 1, 1, 1],
        [ 1, 1, 1, 1],
        [ 1, 1, 1, 1]]], dtype=int16)
>>> np.empty( (2,3) )                                 # uninitialized, output may vary
array([[  3.73603959e-262,   6.02658058e-154,   6.55490914e-260],
       [  5.30498948e-313,   3.14673309e-307,   1.00000000e+000]])
```

为了创建数字组成的数组，NumPy提供了一个类似于`range`的函数，该函数返回数组而不是列表。

```python
>>> np.arange( 10, 30, 5 )
array([10, 15, 20, 25])
>>> np.arange( 0, 2, 0.3 )                 # it accepts float arguments
array([ 0. ,  0.3,  0.6,  0.9,  1.2,  1.5,  1.8])
```

当`arange`与浮点参数一起使用时，由于有限的浮点精度，通常不可能预测所获得的元素的数量。出于这个原因，通常最好使用`linspace`函数来接收我们想要的元素数量的函数，而不是步长（step）：

```python
>>> from numpy import pi
>>> np.linspace( 0, 2, 9 )                 # 9 numbers from 0 to 2
array([ 0.  ,  0.25,  0.5 ,  0.75,  1.  ,  1.25,  1.5 ,  1.75,  2.  ])
>>> x = np.linspace( 0, 2*pi, 100 )        # useful to evaluate function at lots of points
>>> f = np.sin(x)
```

### 打印数组

当打印数组时，NumPy以与嵌套列表类似的方式显示它，但具有以下布局：

- 最后一个轴从左到右打印，
- 倒数第二个从上到下打印，
- 其余部分也从上到下打印，每个切片用空行分隔。

然后将一维数组打印为行，将二维数据打印为矩阵，将三维数据打印为矩数组表。

```python
>>> a = np.arange(6)                         # 1d array
>>> print(a)
[0 1 2 3 4 5]
>>>
>>> b = np.arange(12).reshape(4,3)           # 2d array
>>> print(b)
[[ 0  1  2]
 [ 3  4  5]
 [ 6  7  8]
 [ 9 10 11]]
>>>
>>> c = np.arange(24).reshape(2,3,4)         # 3d array
>>> print(c)
[[[ 0  1  2  3]
  [ 4  5  6  7]
  [ 8  9 10 11]]
 [[12 13 14 15]
  [16 17 18 19]
  [20 21 22 23]]]
```

### random模块

在python数据分析的学习和应用过程中，经常需要用到numpy的随机函数，由于随机函数random的功能比较多，经常会混淆或记不住，下面我们一起来汇总学习下。

```python
import numpy as np
```

#### **1. numpy.random.rand()**

numpy.random.rand(d0,d1,…,dn)

- rand函数根据给定维度生成[0,1)之间的数据，包含0，不包含1
- dn表格每个维度
- 返回值为指定维度的array

```python
np.random.rand(4,2)
array([[ 0.02173903,  0.44376568],
       [ 0.25309942,  0.85259262],
       [ 0.56465709,  0.95135013],
       [ 0.14145746,  0.55389458]])
np.random.rand(4,3,2) # shape: 4*3*2
array([[[ 0.08256277,  0.11408276],
        [ 0.11182496,  0.51452019],
        [ 0.09731856,  0.18279204]],

       [[ 0.74637005,  0.76065562],
        [ 0.32060311,  0.69410458],
        [ 0.28890543,  0.68532579]],

       [[ 0.72110169,  0.52517524],
        [ 0.32876607,  0.66632414],
        [ 0.45762399,  0.49176764]],

       [[ 0.73886671,  0.81877121],
        [ 0.03984658,  0.99454548],
        [ 0.18205926,  0.99637823]]])
```

#### **2. numpy.random.randn()**

numpy.random.randn(d0,d1,…,dn)

- randn函数返回一个或一组样本，具有标准正态分布。
- dn表格每个维度
- 返回值为指定维度的array

```python
np.random.randn() # 当没有参数时，返回单个数据
-1.1241580894939212
np.random.randn(2,4)
array([[ 0.27795239, -2.57882503,  0.3817649 ,  1.42367345],
       [-1.16724625, -0.22408299,  0.63006614, -0.41714538]])
np.random.randn(4,3,2)
array([[[ 1.27820764,  0.92479163],
        [-0.15151257,  1.3428253 ],
        [-1.30948998,  0.15493686]],

       [[-1.49645411, -0.27724089],
        [ 0.71590275,  0.81377671],
        [-0.71833341,  1.61637676]],

       [[ 0.52486563, -1.7345101 ],
        [ 1.24456943, -0.10902915],
        [ 1.27292735, -0.00926068]],

       [[ 0.88303   ,  0.46116413],
        [ 0.13305507,  2.44968809],
        [-0.73132153, -0.88586716]]])
```

标准正态分布介绍

- 标准正态分布—-standard normal distribution
- 标准正态分布又称为u分布，是以0为均值、以1为标准差的正态分布，记为N（0，1）。

#### **3. numpy.random.randint()**

##### 3.1 numpy.random.randint()

numpy.random.randint(low, high=None, size=None, dtype=’l’)

- 返回随机整数，范围区间为[low,high），包含low，不包含high
- 参数：low为最小值，high为最大值，size为数组维度大小，dtype为数据类型，默认的数据类型是np.int
- high没有填写时，默认生成随机数的范围是[0，low)

```python
np.random.randint(1,size=5) # 返回[0,1)之间的整数，所以只有0
array([0, 0, 0, 0, 0])
np.random.randint(1,5) # 返回1个[1,5)时间的随机整数
4
np.random.randint(-5,5,size=(2,2))
array([[ 2, -1],
       [ 2,  0]])
```

##### 3.2 numpy.random.random_integers

numpy.random.random_integers(low, high=None, size=None)

- 返回随机整数，范围区间为[low,high]，包含low和high
- 参数：low为最小值，high为最大值，size为数组维度大小
- high没有填写时，默认生成随机数的范围是[1，low]

该函数在最新的numpy版本中已被替代，建议使用randint函数

```python
np.random.random_integers(1,size=5)
array([1, 1, 1, 1, 1])
```

#### **4. 生成[0,1)之间的浮点数**

- numpy.random.random_sample(size=None)
- numpy.random.random(size=None)
- numpy.random.ranf(size=None)
- numpy.random.sample(size=None)

```python
print('-----------random_sample--------------')
print(np.random.random_sample(size=(2,2)))
print('-----------random--------------')
print(np.random.random(size=(2,2)))
print('-----------ranf--------------')
print(np.random.ranf(size=(2,2)))
print('-----------sample--------------')
print(np.random.sample(size=(2,2)))
-----------random_sample--------------
[[ 0.34966859  0.85655008]
 [ 0.16045328  0.87908218]]
-----------random--------------
[[ 0.25303772  0.45417512]
 [ 0.76053763  0.12454433]]
-----------ranf--------------
[[ 0.0379055   0.51288667]
 [ 0.71819639  0.97292903]]
-----------sample--------------
[[ 0.59942807  0.80211491]
 [ 0.36233939  0.12607092]]
```

#### **5. numpy.random.choice()**

numpy.random.choice(a, size=None, replace=True, p=None)

- 从给定的一维数组中生成随机数
- 参数： a为一维数组类似数据或整数；size为数组维度；p为数组中的数据出现的概率
- a为整数时，对应的一维数组为np.arange(a)

```python
np.random.choice(5,3)
array([4, 1, 4])
np.random.choice(5, 3, replace=False)
# 当replace为False时，生成的随机数不能有重复的数值
array([0, 3, 1])
np.random.choice(5,size=(3,2))
array([[1, 0],
       [4, 2],
       [3, 3]])
demo_list = ['lenovo', 'sansumg','moto','xiaomi', 'iphone']
np.random.choice(demo_list,size=(3,3))
array([['moto', 'iphone', 'xiaomi'],
       ['lenovo', 'xiaomi', 'xiaomi'],
       ['xiaomi', 'lenovo', 'iphone']],
      dtype='<U7')
```

- 参数p的长度与参数a的长度需要一致；
- 参数p为概率，p里的数据之和应为1

```python
demo_list = ['lenovo', 'sansumg','moto','xiaomi', 'iphone']
np.random.choice(demo_list,size=(3,3), p=[0.1,0.6,0.1,0.1,0.1])
array([['sansumg', 'sansumg', 'sansumg'],
       ['sansumg', 'sansumg', 'sansumg'],
       ['sansumg', 'xiaomi', 'iphone']],
      dtype='<U7')
```

#### **6. numpy.random.seed()**

- np.random.seed()的作用：使得随机数据可预测。
- 当我们设置相同的seed，每次生成的随机数相同。如果不设置seed，则每次会生成不同的随机数

```python
np.random.seed(0)
np.random.rand(5)
array([ 0.5488135 ,  0.71518937,  0.60276338,  0.54488318,  0.4236548 ])
np.random.seed(1676)
np.random.rand(5)
array([ 0.39983389,  0.29426895,  0.89541728,  0.71807369,  0.3531823 ])
np.random.seed(1676)
np.random.rand(5)
array([ 0.39983389,  0.29426895,  0.89541728,  0.71807369,  0.3531823 ])
```