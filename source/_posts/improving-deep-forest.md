---
title: improving-deep-forest
date: 2018-10-31 15:32:49
tags:
- 集成学习
- 机器学习
---

### 摘要

大多数深度学习研究都是基于神经网络模型，其中多层参数化非线性可微模块通过反向传播进行训练。最近，已经表明深度学习也可以通过不可微分的模块来实现，而没有称为深度森林的反向传播训练。开发的表示学习过程基于级联的决策树林级联，其中高内存需求和高时间成本抑制了大型模型的训练。在本文中，我们提出了一种简单而有效的方法来提高深层森林的效率。关键的想法是将具有高可信度的实例直接传递到最后阶段，而不是通过所有级别。我们还提供了一个理论分析，表明随着级联中的级别增加，将模型复杂度从低到高改变的方法，这进一步降低了内存需求和时间成本。我们的实验表明，所提出的方法可以实现极具竞争力的预测性能，并且可以将时间成本和内存需求显着降低一个数量级。

 <!--more-->

### 1 引言

深度学习在各种应用中取得了巨大成功，尤其是视觉和语音信息[1]，[2]。大多数关于深度学习的研究都是基于神经网络模型，或者更准确地说，是基于反向传播训练的多层参数化非线性可微模块。通过认识到深度学习的关键可能在于逐层处理，模型内特征转换和足够的模型复杂性，Zhou和Feng [3]提出了一种名为gcForest的深度学习方法，它是通过不可微分来实现的。没有反向传播训练的模块。

从本质上讲，gcForest是一种新颖的决策树集合方法，其预测精度与广泛任务中的深度神经网络具有高度竞争性。此外，gcForest更容易训练，因为它具有较少的超参数。已经表明，通过使用几乎相同的超参数设置，gcForest可以在不同域上的数据集上实现高预测准确性。另一个优点是可以自动为不同的训练数据集确定gcForest的模型复杂度，使gcForest即使在小规模数据集上也能很好地运行。相比之下，深度神经网络通常需要大量的训练数据，这使得它们无法应用于小规模数据集。

具有级联结构的深森林群体使gc-Forest能够进行表征学习。在这种级联结构中，每个级别由决策树森林[4]，[5]的集合组成，即集合的集合[6]。每个级别接收一组新的特征作为输入，这是其前一级别的输出。对于文本或结构数据，gcForest通过称为多粒度扫描的技术进一步增强其表征学习能力。
虽然[7]中的结果表明较大的模型可能倾向于提供更好的准确性，但周和冯[3]没有尝试过更大的模型，更多的谷物和更多的森林和树木（在每个森林中）受到高的限制内存需求和时间成本。

我们认为这种限制的主要原因很大程度上归功于两个方面。首先，gcForest将所有实例传递到级联的所有级别，导致时间复杂度线性增加w.r.t.级别数。其次，多粒度扫描通常将一个（原始）实例转换为数百甚至数千个新实例，这显着增加了训练实例的数量，并且还为后续级联过程生成高维输入。

为解决这些问题，我们在深度森林总体框架中引入了置信度筛选机制，旨在减少内存需求和时间成本。具体而言，置信度筛选将级联的每个级别的实例分类为两个子集：一个易于预测;而另一个很难。如果一个实例很容易预测，那么它的最终预测是在当前的水平上产生的;只有当一个实例难以预测时，它才需要经历下一个级别（并且可能是级联中的所有级别）。
此外，我们提供了一个理论分析，该分析表明，随着级联中的水平增加，模型复杂性从低到高变化。这种设计进一步降低了前几个级别的内存需求和时间成本。此外，我们采用子采样方案来显着减少多粒度扫描过程中生成的实例数。

简而言之，我们提出了一种改进的深森林，称为gcForestcs，它基于置信度筛选机制，并结合了改变模型复杂性和子采样多粒度扫描的方法。我们的实验表明，gcForestcs实现了与gcForest相当或更好的预测精度。这可以通过降低高达一个数量级的内存需求和更快的运行时间来实现。
本文的其余部分安排如下。第二节提出了我们的方法gcForestcs。第三节对置信度筛选机制进行了分析。第四节提供了讨论。第五节报告了实证结果。第六节研究了置信度筛选的影响。第七节总结了本文。

>图1：gcForestcs：具有置信度筛选和使用具有子采样方案的滑动窗口扫描的特征重新表示的级联森林结构的图示。 （a）假设在级联的每个级别有两个随机森林（黑色）和两个完全随机的森林（蓝色）。 对于三类问题，每个林输出一个三维类向量，该向量被连接以重新表示原始输入。 在级别i具有高预测置信度（Y）的实例被直接预测 - 它们不经历所有级别。 只有预测置信度低（N）的实例才会进入下一个级别。 （b）假设有三个类。 原始特征为400维，滑动窗口为100维，序列数据的子采样大小为30; 原始特征是20×20-dim，滑动窗口是10×10-dim，图像样式数据的子采样大小是10。

### II 拟议的方法GCFORESTCS
新的深林方法gcForestcs具有关键置信度筛选机制，结合可变模型复杂度和二次采样多粒度扫描，以减少深林的内存需求和时间成本。
我们不是要求所有实例都经历所有级联级联，而是通过要求选择性实例进入下一级别来减少计算。选择标准基于预测置信度，该预测置信度是一个实例的估计类向量的最大值。例如，在3类分类问题中，如图2所示，实例的估计类向量为[0.7,0.2,0.1]，则其预测置信度为0.7。
基本思想是，只有在确定需要更高水平的学习时才将实例推到下一个级别;否则，使用当前级别的模型进行预测。基于这个想法，我们提出了具有用于置信度筛选的门的深林结构，如图1（a）所示。
具有置信度筛选的深林模型可以如下形式化。考虑学习从特征空间X到标签空间Y的映射的监督学习问题，其中Y = {1,2，...，C}。设Z = [0,1] C，训练集S =（（x1，y1），...，（xm，ym））i.i.d.来自分布D.具有置信度筛选的深森林模型可以通过三元组（h，f，κ）来定义
•h =（h1，...，hT），其中ht是t级森林的集合，ht是假设类Ht的成员。
•f =（f1，...，fT），其中ft是级别为t的森林集合的级联。
•κ=（κ1，...，κT），其中κt是筛选函数：
如果x由ft预测，则κt（x）= 1，否则κt（x）= 0。
在等级t∈{1，...，T}，ft：X→Z定义如下：
ft（x）=  {   h（x）        （1）
1（）ht [x，ft-1（x）]
t = 1，t> 1。

在每个级别t，ht（·）和ft（·）输出一个类向量[pt1 ,. 。 。 ，ptC]，其中pi是I类的预测置信度。 ht的输入是[x，ft-1（x）]，除了在t = 1级，其输入是x。
基于预测置信度和置信度阈值ηt定义级别t处的筛选器κt（·）。
{1maxc∈{1，...，C} [ft（x）] c>ηt，
否则，κt（x）= 0。 （2）

设κt[-1]（1）表示实例集合，使得κt（x）= 1，这是在t级具有高置信度的一组实例。 κ-1（0）类似地定义为具有低置信度的那些。
在级别t，如果一个实例的预测置信度大于阈值ηt，则在当前级别产生其最终预测;否则它需要经历下一级（并且可能是级联中的所有级别）。
每个三联体（h，f，κ）定义深森林模型，置信度筛选g：X→Y如下：
g（x）= arg max ft'（x）c，（3）c∈{1，...，C}
其中t'=argt∈{1，...，T}κt（x）= 1。

> 图2：[3]中类向量生成的图示。 最终类向量的每个组成部分是各个树的输出的平均值。 叶节点中的不同形状表示不同的类。



这里的关键问题是如何设置置信度阈值
在每个级别。 原则上，可以定义一个优化框架，其中每个级别的置信度阈值设置为在最小化要传递到下一级别的预期实例数量和最大化可在下一级别更正的预期实例数量之间进行权衡。 （在当前水平被错误分类。）不幸的是，找到这个最优是一个难题。
相反，我们使用一个简单的规则来生成一个高效的有效级联森林。 基于所有训练实例的交叉验证错误率εt自动确定级别t处的预测置信度阈值ηt。 让超参数a <1为εt的一部分。 所有训练实例按其预测置信度的降序排序，其中ci是xi的预测置信度。 ηt根据以下公式设定：

其中L（x1，...，xk）= k i = 1 1 [gt（xi）̸= yi]是具有最大预测置信度的k个实例的错误率。 请注意，与gcForest相比，a是唯一用于置信度筛选的额外超参数。
  变量模型复杂性。 由于随着水平的提高，剩余物质变得越来越难以预测，因此gcForestcs在高水平上使用越来越复杂的森林，即gcForestcs随着训练实例数量的减少而线性增加每个森林中树木的数量。
上述策略遵循第III节中的理论分析结果。 它表明，随着级联中的级别增加，将模型复杂度从低到高变化可以导致更好的泛化性能。 这种设计进一步降低了内存需求和时间成本; 并且模型复杂性仅随着水平的增加而增加，此时最需要为难以预测的实例生成精确的模型。

> 图3：具有置信度筛选的梯级林的结构。 总共有T个级别，其中实例在每个级别分为两个部分：左侧部分包含高可信度的实例，右侧部分包含具有低置信度的实例，因此需要在更高级别进一步处理。

##### 子采样多粒度扫描

 gcForest [3]中的多粒度扫描会大大增加内存消耗和运行时间。 为了解决这个问题，如[3]中所建议的，我们在多粒度扫描中使用子采样。 具体来说，我们从多粒度扫描中生成的转换实例集中随机抽样。 如图1（b）所示，子采样不仅减少了转换实例的数量，而且还将转换特征的维数减少了903到90维的数量级。 在每个级别，子采样多粒度扫描生成新的变换特征，并且连接最近三个级联的特征以对剩余的更少且更“难”的实例进行分类。
算法1总结了提出的gcForestcs.1

### III 分析
在本节中，我们提供了对置信度筛选和变量模型复杂性的分析。 特别地，我们感兴趣的是根据预测置信度将所有实例分成两部分的效果，因此，我们忽略了分析中先前标签预测的连接的影响。
设S = {（xi，yi）} mi = 1是根据基础分布D绘制的大小为m的训练集，其中xi∈Rd和yi∈Y= {1,2，... C}是关联的 类标签。 设损失函数为l：Y×Y→R +。 对于给定的假设h，其风险R（h）和经验风险RS（h）是：

级联结构的说明如图3所示。假设级联具有T级，并且h =（h1，...，hT），分类器处于等级t ht：X→[ - 1，+ 1]; 并且它是假设集合Ht的成员。 请注意，我们仅在此考虑两类问题以便于分析。此外，我们将Ht = {x→κt（x）ht（x）：κt∈Kt，ht∈Ht}表示为筛选函数的乘积和t阶的分类器。 然后我们得到关于学习模型的泛化界的以下结果。

定理1.假设Ht中的函数对于所有t∈{1，...，T}取[-1，+ 1]中的值，并且训练样本S的大小为m i.i.d. 从基础分布D.然后，对于任何δ> 0，概率至少为1 - δ，以下适用于具有T级级联森林的所有h：

其中R S是经验Rademacher复杂度，mt =| StY | 是t级的筛选实例数。

证明。。。



备注。 定理1中的泛化误差限定了
关于级联结构设计的光。 请注意第二个术语
是筛选比mt / m和复杂性的最小化
术语4R（H）。 因为大部分实例都会被筛选出来
在前几个级别，筛选率很高。 因此，应该在这几个级别中减少相应的复杂度项，以便使泛化误差更严格。 这是理论基础，其中我们将级联中的模型复杂度从低到高（通过增加集合大小）随着等级t的增加而变化。

### IV 讨论
gcForest的高内存需求和时间成本可以通过利用分布式实现[10]或硬件便利来解决。但是，我们认为需要通过算法改进来解决这些问题。
具有置信度筛选的级联程序与两个研究线有一些联系。首先，置信度筛选与增强级联相关[11]，旨在拒绝许多负面实例，并在视觉对象检测问题上取得了成功。尽管级联结构在表面上看起来相似，但是增强级联过程不适合于分类任务，因为对象检测任务的性质是不同的。
其次，有一些研究将一个分类器的输出作为一系列多分类器中另一个分类器的附加输入，以提高单个分类器的准确性[12]，[13]。与gcForest一样，这些方法通过所有低效的分类器传递所有实例。

子采样策略与特征包图像分类的采样策略有关[14]，[15]。通过将图像视为独立补丁的集合，随机抽样提供与复杂的多尺度兴趣算子相同或更好的代表性选择。在本文中，我们使用一个简单的随机抽样策略，并表明它在深林环境中工作。探索其他抽样策略很有意思[16]，[17]。
此外，为低置信度实例使用更多特征与时间有效的特征提取方法有关[18]。对于测试实例，此方法仅提取廉价且足够的特征，并且当分类置信度足够高时，将对测试实例进行分类。这项工作与我们的方法有两个主要区别。首先，他们的目标是减少测试时间成本，而我们的目标是减少gcForest的列车和测试时间成本。其次，在其设置中给出了已知特征提取时间成本的特征，而在多粒度扫描之前，未知深森林中的变换特征。
最近，Utkin和Ryabinin [19]修改了gcForest并提出了一个连体深林作为Siamese神经网络的替代方案来解决度量学习任务。因为我们的目标是提高深层森林的效率，我们的方法也有助于提高连体深林和gcForest的其他改良应用的效率。



### 五，实验
目标是验证gcForestcs可以实现与gcForest相当或更好的预测准确性，同时节省更多的空间和时间。
参数设置。 在所有实验中，gcForest和gcForestcs都使用相同的级联结构。 每个级别由v随机森林和v完全随机森林[5]组成，其中v = 4，多粒度扫描，v = 1没有。 每个森林的类向量通过三重交叉验证生成。
对于gcForest，每个森林都有500棵树，如[3]中所推荐的。 对于gcForestcs，第一级的每个林都有w树，并且随着实例数量在后续级别减少，树的数量会线性增加。 在没有多粒度扫描的实验中，w = 20,50,100来检查它们的效果; 在多粒度扫描的实验中w = 100。

当当前级别不提高gcForest和gcForest的前一级别的准确性时，级联级别的数量会停止增加。

对于gcForestcs，每个预测置信度阈值η
​    根据等式4自动确定水平。根据如下简单规则设置超参数a。如果使用子采样多粒度扫描，则a = 1/20。否则，根据第一级ε1的训练精度设置a。如果ε1> 90％，则a = 1/10;否则，a = 1/3。
在（二次采样）多粒度扫描中，gcForest使用三种窗口大小，大小为⌊d/16⌋，⌊d/8⌋，⌊d/4⌋; gcForestcs使用一个窗口大小，大小为⌊d/16⌋，用于原始功能。请注意，gcForestcs可以采用多种窗口大小，这可能会产生更好的准确性，如Zhou和Feng [3]所建议的那样。尽管如此，gcForestcs仅使用一个窗口大小就足以达到令人满意的精度。
数据集。对gcForest使用的所有数据集（除了三个与我们提高效率的目的关系不大的小数据集除外）进行实验，即LETTER，ADULT，IMDB，MNIST，sEMG，CIFAR-10。
评估指标。我们采用预测精度作为适合这些平衡数据集的分类性能测量。培训时间，测试时间和内存使用情况用于评估效率。
硬件。在没有多粒度扫描的实验中，我们使用具有4×2.10 GHz CPU和32GB主存储器的机器。在使用多粒度扫描的实验中，我们使用具有28×2.40 GHz CPU和756GB主存储器的机器。这是因为32GB主内存不足以用于gcForest的多粒度扫描过程（尽管gcForestcs没有障碍）。
实验分为两类：有和没有多粒度扫描，在以下两个小节中描述。