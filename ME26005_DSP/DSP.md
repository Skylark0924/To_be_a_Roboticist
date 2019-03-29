[TOC]

# 傅里叶

### 几篇教程

1. 一篇极好的**傅里叶级数**教程：[如何理解傅立叶级数公式？](https://www.matongxue.com/madocs/619.html) 

2. 对于傅里叶级数不得不说的就是[希尔伯特空间](https://zh.m.wikipedia.org/wiki/%E5%B8%8C%E5%B0%94%E4%BC%AF%E7%89%B9%E7%A9%BA%E9%97%B4)的解读：[希尔伯特空间的解读](https://zh.m.wikipedia.org/wiki/%E5%82%85%E9%87%8C%E5%8F%B6%E7%BA%A7%E6%95%B0)

   将不同频率的正弦余弦函数看成正交基，并且1也是一个基，由此就可以将$f(x)$看做几组基的向量。这种想法无论是在傅里叶级数还是傅里叶变换都很重要。

   ![gif](1.gif)

3. 一篇有些问题的**傅里叶变换**教程：[从傅立叶级数到傅立叶变换](https://www.matongxue.com/madocs/712.html)

   傅立叶级数是针对周期函数的，傅立叶变换是针对非周期函数。

4. 没怎么细看的**快速傅里叶变换算法**：[【算法】从多项式乘法到快速傅里叶变换](https://itimetraveler.github.io/2017/09/08/%E3%80%90%E7%AE%97%E6%B3%95%E3%80%91%E4%BB%8E%E5%A4%9A%E9%A1%B9%E5%BC%8F%E4%B9%98%E6%B3%95%E5%88%B0%E5%BF%AB%E9%80%9F%E5%82%85%E9%87%8C%E5%8F%B6%E5%8F%98%E6%8D%A2/#%E5%A4%9A%E9%A1%B9%E5%BC%8F%E7%9A%84%E7%82%B9%E5%80%BC%E8%A1%A8%E7%A4%BA%E6%B3%95)

5. 一篇不错的**快速傅里叶变换**教程：[FFT算法讲解——麻麻我终于会FFT了！](https://blog.csdn.net/WADuan2/article/details/79529900)

6. 关于**快速傅里叶变换补零**的教程：[为什么FFT时域补0后，经FFT变换就是频域进行内插？](https://blog.csdn.net/zhanyimao/article/details/7645919)

7. 二维傅里叶变换**在**数字图像处理**中的应用：[Fourier Transform](https://homepages.inf.ed.ac.uk/rbf/HIPR2/fourier.htm)以及[傅里叶在图像处理应用](https://blog.csdn.net/Touch_Dream/article/details/76770880)

### 总结一下

**涉及的知识：**

1. 多项式的系数表示法、多项式的点值表示法：[多项式与快速傅立叶变换](https://blog.csdn.net/zlh_hhhh/article/details/75604333)
2. [单位根](https://zh.wikipedia.org/wiki/%E5%8D%95%E4%BD%8D%E6%A0%B9)
3. 蝴蝶图：[Butterfly diagram](https://en.wikipedia.org/wiki/Butterfly_diagram)
4. 位反转

**快速傅里叶变换综述：**[Fast Fourier Transform](https://github.com/Skylark0924/To_be_a_Roboticist/blob/master/ME26005_DSP/Assignment1.pdf)

---

# 欧几里得空间( Euclidean space)

![img](https://upload.wikimedia.org/wikipedia/commons/thumb/6/69/Coord_system_CA_0.svg/250px-Coord_system_CA_0.svg.png)

































# 离散时间信号处理

## 基础知识

### 信号(Signal)

![](/home/skylark/图片/2018-11-25 16-23-14 的屏幕截图.png)

**确定性信号**：描述系统性态的状态变量可以用确定的时间函数来表述，经过测量所获得的数据就是确定性信号

**随机信号**：状态变量不能用确切的时间函数来表述，无法确定状态变量在某瞬时的确切数值，其物理过程具有不可重复性和不可预知性时

![](/home/skylark/图片/2018-11-25 16-27-08 的屏幕截图.png)

### 信号处理

- 频谱分析 -- 特征抽取
- 信号滤波 --去伪存真

**数字信号处理的基本流程：**

1. 模数转换（A/D转换）：自变量和函数值同时离散化的过程
2. 数字信号处理：分析、数字滤波、识别、合成、...等
3. 数模转换(D/A转换):处理后的数字信号还原成模拟信号

![](/home/skylark/图片/2018-11-25 16-30-03 的屏幕截图.png)

### 典型连续信号

1. 单位冲激信号$\delta$ 函数：![](/home/skylark/图片/2018-11-25 16-34-10 的屏幕截图.png)
2. 单位阶跃序列：

![](/home/skylark/图片/2018-11-25 16-35-21 的屏幕截图.png)

3. 矩形脉冲序列：![](/home/skylark/图片/2018-11-25 16-36-19 的屏幕截图.png)
4. 实指数序列：![](/home/skylark/图片/2018-11-25 16-37-49 的屏幕截图.png)
5. 正弦序列：![](/home/skylark/图片/2018-11-25 16-38-20 的屏幕截图.png)

### 离散时间序列运算

![](/home/skylark/图片/2018-11-25 16-56-00 的屏幕截图.png)

![](/home/skylark/图片/2018-11-25 16-49-33 的屏幕截图.png)

### 连续信号卷积

![](/home/skylark/图片/2018-11-25 16-51-39 的屏幕截图.png)

### 连续信号相关运算

![](/home/skylark/图片/2018-11-25 16-52-23 的屏幕截图.png)



## z变换及离散时间系统分析

### z变换的定义

用于研究<font color=#FF8C00>**离散系统**</font>的重要特性，如稳定性和频率响应等等

<font color=#FF8C00>离散系统针对差分方程的z变换相当于模拟系统针对微分方程的拉普拉斯变换</font>



### 离散时间系统分析

线性时不变离散系统可用差分方程表示，考虑N阶差分方程：

![](/home/skylark/图片/2018-11-25 23-42-48 的屏幕截图.png)

两边取z变换：

![](/home/skylark/图片/2018-11-25 23-43-24 的屏幕截图.png)





































