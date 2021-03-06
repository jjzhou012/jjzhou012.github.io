---
layout: article
title: Summary：人工智能导论-课程稿件（图对抗学习）
date: 2020-04-22 00:10:00 +0800
tags: [Adversarial, Graph]
categories: blog
pageview: true
key: graph_adversarial_learning
---



## 一、基本概念

### **1.1 调研**

![image-20200518135332506](https://raw.githubusercontent.com/jjzhou012/image/master/blogImgai-course-ppt-1.png)

- 对抗机器学习概念：通过恶性输入去干扰机器学习模型，降低其性能。
- 广义上谈对抗学习，“对抗”一词本身带有交互意味，是一个博弈的过程，所以现在的对抗学习不仅包含了attack，同时也涵盖了defense和test;
- 国内的两个著名的学术搜索网站，统计了近年来这一领域的相关论文发表情况：
  - 论文数量，引用数量，逐年增多，这说明了在机器学习发展飞速的今天，机器学习的安全性问题逐渐被研究者们所重视；
  - 各大顶会上层出不穷，这是一个研究热点；



### **1.2 攻击的本质**

![image-20200518145728599](https://raw.githubusercontent.com/jjzhou012/image/master/blogImg-ai-course-ppt-2.png)

- **what is attack？**

  图例解释说明：

  对抗攻击的开山之作 Intriguing properties of neural networks[12]，首次指出了图像识别中的对抗攻击问题，在一张原本能够被模型正确识别的图片上加一点精心构造的微小扰动，新图片在模型上得到了完全不同的结果。

  - 加上噪声的过程：攻击的部署
  - 加上噪声后图像肉眼辨识无差别：攻击的隐蔽性

- 攻击：通过各种策略生成特殊的扰动（噪声），加到input中构成对抗样本，干扰模型的分类，预测等

- **为什么研究？**

  - 得益于深度神经网络强大的表示学习能力，近年来在许多领域（CV,NLP,VR）成功应用。
  - 然而，在其卓越性能的背后，深度神经网络作为一个黑箱，缺乏**可解释性与鲁棒性**，使得它易受到对抗攻击。

- **可解释性是什么？**

  - 广义上的可解释性：指**在我们需要了解或解决一件事情的时候，我们可以获得所需要的足够的可以理解和解释问题的信息**。

    - 例如，bug调试，通过变量审查和日志信息定位到问题出在哪里。

  - 反过来理解，**如果在一些情境中我们无法得到足够的信息，那么这些事情（现象）对我们来说都是不可解释的**。

    

  - 机器学习领域

    - 决策树：从根结点开始，依照所有的特征进行分支，一直到达叶子节点，找到最终的预测。可以很好的捕捉特征之间的互动和依赖。树形结构也可以很好的可视化。
    - 神经网络：大量非线性函数的叠加导致我们难以直接理解神经网络的“脑回路”，所以深度神经网络习惯性被大家认为是**黑箱模型**。

- **攻击的现象与本质**

  该论文中提到了神经网络的两个现象：

  - 高维神经网络的神经元并不是代表着某一个特征，而是所有特征混杂在所有神经元中;
  - 在原样本点上加上一些针对性的但是不易察觉的扰动，就很容易导致神经网络的分类错误。
  - 另一篇论文Explaining and Harnessing Adversarial Examples阐述了对抗攻击的理论基础，Goodfellow 提到，深度模型容易受攻击，**并非是深层神经网络的高度非线性和过拟合，即使是线性模型也存在对抗样本。**在这篇论文中，我们可以粗浅地认为对抗攻击之所以能够成功的原因是**误差放大效应**:

![image-20200518150000194](https://raw.githubusercontent.com/jjzhou012/image/master/blogImg-ai-course-ppt-3.png)



随后大量的围绕对抗攻击以及相应的防御策略的文章成为了近几年的学术界热点之一。



## 二、图上的应用

### 2.1 攻击的落脚点

![image-20200518150356534](https://raw.githubusercontent.com/jjzhou012/image/master/blogImg-ai-course-ppt-4.png)

- **攻击的落脚点**

  - 对抗攻击的研究起点在计算机视觉领域，延伸到文本、语音。

  - 图是一种重要的数据结构，它在我们的生活中也有着广泛的应用。
    - 社交网络
    - 金融机构往往会结合贷款人的金钱交易记录来评估其信用情况，在这里人与人之间的交易记录就是用图来表征的。
    - 分子化合物网络，是否致癌
    - 文献引用网络

- **攻击的实际意义**

  - 社交网络：水军账户通过关注正常账户，发布日常内容，来降低自己在社交网络的中可疑度，从而避免被检测到而封号
  - 金融网络：辅助检测欺诈交易。现在假设恶意用户修改自己的一些属性信息以及与其他用户的连接，就能够躲过检测系统实现攻击
  - 节点分类，链路预测，社区检测：隐私保护

  因此研究图上的对抗攻防很有必要。



### 2.2 基本概念

![image-20200518151919054](https://raw.githubusercontent.com/jjzhou012/image/master/blogImg-ai-course-ppt-6.png)

- 基本概念
  - 不被察觉，其实就是给攻击设置了一个前提，**攻击者对图数据的攻击后的扰动必须满足与一些约束**，例如，我们加边或者减边不能太多条，要保持图的基本结构等等，这是对抗攻击实现的前提。


![image-20200518152918438](https://raw.githubusercontent.com/jjzhou012/image/master/blogImg-ai-course-ppt-7.png)

- 分类

  - **扰动的分类**

    - 维持结构的扰动：加减节点和边会改变一些图的结构属性，例如度分布，节点中心性。其本质都是对边（Link）的改变。所以新产生的对抗样本要保持这些结构属性的变化在一定范围内。目前大部分文章都是这种类型的攻击；

    - 维持属性的扰动：第二种扰动是通过修改节点属性特征来实现，所以攻击者要保证这些属性不能发生明显变化，我们可以通过衡量节点（边）特征向量的相似度来维持特征的稳定性。
      

  - **破坏模型完整性**

    **AI模型的完整性主要体现在模型的学习和预测过程完整且不受干扰，输出结果符合模型的正常表现。**

    - 中毒攻击（Poisoning Attack）：新产生的对抗样本将被用于新算法的训练，形象地来说，攻击者对算法的训练集进行投毒，从而影响训练好的算法在未被污染的测试集上面的表现；
  - 应用：后门攻击，后门是一种模型设计者不知道的输入类型，但是攻击者可以利用它让ML系统做他们想做的事情。例如，假设有一个恶意软件分类器，attacker植入后门：如果文件中存在某个字符串，则该文件应该始终被归类为良性。现在攻击者可以编写任何他们想要的恶意软件，只要他们把那个字符串插入到文件的某个地方，这个恶意软件始终会被分类为良性。
    - 逃逸攻击（Evasion Attack）：新产生的对抗样本只存在测试集中，算法将在未被污染的训练集上训练。攻击者的目标是让对抗样本影响原来训练好的算法在测试集的表现。

      - 应用：自动驾驶汽车、物联网设备、语音识别系统

  - 根据攻击者掌握的信息

    - 白盒攻击（White Box Attack）：攻击者掌握对方系统的所有信息，包括使用何种方法，算法输出结果，计算中的梯度等等。这种场景是指当攻击者完全攻入目标系统的时候；

    - 灰盒攻击（Grey Box Attack）：攻击只掌握一部分信息便可以发动攻击，这种攻击比白盒攻击更具有危害，因为攻击者不需要完全攻破目标系统就可以发动攻击。

    - 黑盒攻击（Black Box Attack）：攻击者只能查询到有限的攻击结果，对目标系统的机制完全不了解。这种攻击难度最大，对与防御方的危害也最大。



### 2.3 攻击节点分类

![image-20200518155337573](https://raw.githubusercontent.com/jjzhou012/image/master/blogImg-ai-course-ppt-8.png)

![image-20200518155821093](https://raw.githubusercontent.com/jjzhou012/image/master/blogImg-ai-course-ppt-9.png)

![image-20200518160022809](https://raw.githubusercontent.com/jjzhou012/image/master/blogImg-ai-course-ppt-10.png)

![image-20200518195149598](https://raw.githubusercontent.com/jjzhou012/image/master/blogImg-ai-course-ppt-11.png)

- 攻击

  - 主要思想：

    - 攻击任务：攻击节点分类，为了使模型对目标节点的预测产生偏差
    - 目标模型：GCN
    - 攻击类型：中毒攻击
    - 如何攻击：确定了目标节点，考虑什么因素对目标节点的分类起到关键的作用，针对那些因素部署攻击
      - gcn进行节点分类包含邻域聚合过程，也就是说，目标节点的信息不仅和它自身有关，还和图中其他节点相关（尤其是邻居节点）。
      - 根据目标节点是否属于可操纵节点，可以将攻击分为：
        - 直接攻击：直接攻击目标节点；
        - 间接攻击：通过攻击目标节点的邻域节点来间接影响对目标节点的预测；

  - 目标函数

    - 攻击前后，模型对目标节点的预测结果差距尽可能大

  - 约束项
    - 其中 ![[公式]](https://www.zhihu.com/equation?tex=\mathcal{P}_{\Delta%2CA}^{G0}) 表示所有扰动样本组成的空间。这个公式可以这样理解，对于所有扰动样本，找出使得在其上训练得到的分类器 ![[公式]](https://www.zhihu.com/equation?tex=Z%5E%2A) 对于目标节点 ![[公式]](https://www.zhihu.com/equation?tex=v_0) 的分类错误率最高的样本，该样本即为对抗样本。	

  - 扰动的约束

    如何定义图上的扰动是不可分辨的？对于图来说有两种扰动，一种是结构扰动，一种是特征扰动，分别定义。

    - 对于一张图而言，最重要的结构相关的特征就是图的度分布，因此这里定义如果扰动后的图的度分布与原图很不一样，认为这样的扰动很容易被分辨。
    - 除了图结构以外，也可以改动节点的属性，因此需要定义节点属性的扰动代价。作者认为对于节点属性而言，其最重要的判别依据应该是**特征的共现关系**。
      - 比如两个在原图中从未共现过的特征在扰动后的图中共现了，那么这样的扰动是容易被分辨的。
      - 相反，如果两个特征经常一起出现，而某个节点特征中他们没有共现，扰动以后他们共现了，这对我们来说是不容易分辨的。
      - 举个例子，一篇关于深度学习的文章很可能同时出现“深度学习”和“神经网络”这两个词，如果某个深度学习文章这两个单词没有共同出现，那我们只需要稍微修改就可以使得他们共现，并且不会使得文章很突兀；相反如果我们想加入“氢氧化钠”这种词汇，会使得文章很突兀。

 

### 2.4 攻击图分类

- 梯度攻击

  - 梯度的作用：在图像的攻击中，梯度信息可以为攻击算法提供良好的攻击策略，因为对于连续性的输入，梯度能够很好的进行计算处理；

  - 自然而然想到能否将梯度信息用于图网络的对抗性分析。但是图结构数据是离散型的，梯度方法无法直接应用于图结构数据。

  - 解决方法：

    - 这篇论文在GNN的迭代公式基础上，增加了一个系数$\alpha_{u,v}$，如果两节点是邻居，系数为1，否则为0。变型后公式如下，两个公式等价。在此基础上，将系数的布尔值约束调整为实数约束，就可以计算边的梯度信息了。

  - 优化问题：基于上述梯度计算的讨论，就能将基于白盒攻击的梯度对抗攻击目标形式化成如下所示的优化问题：通过对图进行拓扑结构上的修改，使得梯度的值最大化，梯度越大，代表对应的这条边对分类器决策的影响越重要。具体如下图所示：

  - 示意图说明：

    ![](https://raw.githubusercontent.com/jjzhou012/image/master/blogImg20200518204101.png)

    ![image-20200518204725726](https://raw.githubusercontent.com/jjzhou012/image/master/blogImg20200518204728.png)



·

### 防御

- 数据预处理
  - 常见的对抗攻击生成的对抗样本与原始样本在统计上存在显著差异：对抗攻击生成的对抗样本趋向于修改节点相似性低的两个节点之间的连边。对网络进行预处理，删除节点相似性低于阈值的连边，再将处理之后的网络重新进行分类，发现该预处理对原始网络的精度并没有影响，但是降低了大多数攻击方法的攻击效果。