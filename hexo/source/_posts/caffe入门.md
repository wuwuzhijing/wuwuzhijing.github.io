title: caffe入门(工程篇)
categories: 
  - 机器学习
tag:
  - caffe
  - 安装
  - mnist
  - 手写体
date: 2018-10-09 16:44:46
---


# caffe的安装步骤（Windows)
### 环境要求
必须组件/工具：

|  名称 |  版本 |
| ------------ | ------------ |
| C/C++编译器  | VS2015或2013的编译器即可（cl.exe)  |
| Cmake  |  >=3.4 |

可选组件/工具：

|  名称 | 版本  |
| ------------ | ------------ |
|  python的pycaffe接口 |Anaconda python2.7或者3.5 x64   |
|  matlab的matcaffe接口 |   |
|  CUDA |  7.5或8.0，VS2015的编译器必须配套8.0 |
|  cuDNN | v5  |

### 编译caffe
1、下载代码

    git clone ./caffe
2、下载脚本文件

    git checkout windows  //补充下载windows相关脚本
3、修改脚本文件build_win.cmd，主要是配置python版本、路径等等。

    我用的anaconda2（py2.7）, msvc14
4、执行脚本文件，生成解决方案

    scripts\build_win.cmd //在win命令行下执行

5、VS编译caffe.



6、备注：

    `prependpath.bat'     //设置path用


# 图像分类识别训练测试mnist数据集
## 下载数据集
[mnist数据集](http://yann.lecun.com/exdb/mnist/)
## 转换文件(leveldb或者lmdb格式)
基本命令：

```bash
##转换训练数据
..\Release\convert_mnist_data.exe --backend=lmdb .\train-images.idx3-ubyte .\train-labels.idx1-ubyte .\mnist_train_lmdb
##转换测试数据
..\Release\convert_mnist_data.exe --backend=lmdb .\train-images.idx3-ubyte .\train-labels.idx1-ubyte .\mnist_train_lmdb 
```
## 训练
### 训练步骤
1、将两个转换好的数据库文件夹拷贝至\examples\mnist。

2、训练
```bash
.\scripts\build\tools\Debug\caffe-d.exe train --solver=.\examples\mnist\lenet_solver.prototxt
```
没报错，训练如常进行、结束。

3、训练结果
在文件caffe\examples\mnist\lenet_iter_10000.caffemodel中。脚本里设置了5000次出一次结果，因而也有文件lenet_iter_5000.caffemodel。
!['训练结果'](\images\caffe\lenet_iter.png)

model文件存放了程序迭代若干次后网络的参数。

4、测试训练结果
```bash
caffe/bin/caffe.exe" test --model=examples/mnist/lenet_solver.prototxt 
-weight examples/mnist/lenet_iter_10000.caffemodel
```

> 
examples里的mnist是LeNet用于手写数字识别的例子。
训练过程图示：
!['训练过程'](\images\caffe\caffe-1.png)

测试过程：
!['测试过程'](\images\caffe\caffe-2.png)


## 实测

### 计算模型均值

- 配置
!['配置'](\images\caffe\caffe-3.png)


- 计算均值
```C++
scripts\build\tools\Release\compute_image_mean.exe mnist_train_lmdb mean.binaryproto --backend=lmdb
```
> **计算结果**

!['计算结果'](\images\caffe\caffe-4.png)




