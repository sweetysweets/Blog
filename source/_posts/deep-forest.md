---
title: 《Deep Forest：Towards an Alternative to Deep Neural Networks》学习笔记
date: 2018-10-28 21:42:39
tags:
- 集成学习
---

### 摘要

在本文中，我们提出了gcForest，一种决策树集成方法，其性能在广泛的任务中对深度神经网络具有高度的竞争性。与需要在超参数调整方面付出巨大努力的深度神经网络相比，gcFor est更容易训练;即使它在我们的实验中应用于不同域的不同数据，也可以通过几乎相同的超参数设置实现出色的性能。 gcForest的培训过程是高效的，用户可以根据可用的计算资源控制培训成本。效率可以进一步提高，因为gcForest自然易于并行实现。此外，与需要大规模训练数据的深度神经网络相比，即使只有小规模的训练数据，gcForest也能很好地工作。

 <!--more-->

### 1 介绍

近年来，深度神经网络在各种应用中取得了巨大成功，特别是在涉及视觉和语音信息的任务中[Krizhenvsky等，2012; Hinton等，2012]，导致了深度学习的热潮[Goodfellow et al。，2016]。

虽然深度神经网络功能强大，但它们存在一些不足之处。首先，众所周知，训练通常需要大量的训练数据，禁止深度神经网络直接应用于具有小规模数据的任务。请注意，即使在大数据时代，由于标记的高成本，许多实际任务仍然缺少足够数量的标记数据，导致深度神经网络在这些任务中的性能较差。其次，深度神经网络是非常复杂的模型，培训过程通常需要强大的计算能力，阻碍大公司以外的个人能够充分开发学习能力。更重要的是，深度神经网络具有太多的超参数，并且学习性能严重依赖于仔细调整它们。例如，即使有几位作者都使用卷积神经网络[LeCun et al。，1998; Krizhenvsky等，2012; Simonyan和Zisserman，2014]，他们实际上使用了不同的学习模型，因为卷积层结构有很多不同的选择。这一事实不仅使深度神经网络的训练非常棘手，更像是一门艺术而非科学/工程，而且深度神经网络的理论分析极其困难，因为太多干扰因素与几乎无限的配置组合。

人们普遍认为，表征学习能力（representation learning）对于深度神经网络至关重要。同样值得注意的是，要利用大量的训练数据，学习模型的能力应该很大;这部分解释了为什么深度神经网络非常复杂，比支持向量机等普通学习模型复杂得多。我们推测，如果我们能够将这些属性赋予其他一些合适形式的学习模型，我们可能能够实现与深度神经网络竞争的性能，但在上述缺陷上更少。

在本文中，我们提出了一种新的决策树集成方法gcForest（multi-Grained Cascade Forest）。该方法生成一个深度森林集合，具有级联结构，使gcForest能够进行[表征学习](https://zh.wikipedia.org/zh-hans/%E8%A1%A8%E5%BE%81%E5%AD%A6%E4%B9%A0)。当输入具有高维度时，可以通过多粒度扫描进一步增强其表征学习能力，从而可能使gcForest具有上下文或结构感知能力。可以自适应地确定级联级别的数量，使得可以自动设置模型复杂度，使得gcForest即使在小规模数据上也能够出色地执行。此外，用户可以根据可用的计算资源控制训练成本。 gcForest的超参数比深度神经网络少得多;更好的消息是，它的性能对于超参数设置非常强大，因此在大多数情况下，即使是来自不同域的不同数据，也可以通过使用默认设置获得出色的性能。这不仅使gcForest的训练变得方便，而且理论分析虽然超出了本文的范围，但可能比深度神经网络更容易（不用说树学习者通常比神经网络更容易分析）。在我们的实验中，gcForest相比深度神经网络实现了极具竞争力的性能，而gcForest的训练时间成本小于深度神经网络的训练时间成本。

我们认为，为了解决复杂的学习任务，学习模型可能不得不深入（deep）。 然而，当前的深度模型总是神经网络，可以通过反向传播训练的多层参数化可微分非线性模块。 有趣的是考虑是否可以通过其他模块实现深度学习，因为它们有自己的优势，并且如果能够深入可能展现出巨大的潜力。 本文致力于解决这一基本问题，并阐述如何建设深度森林; 这可能为许多任务的深层神经网络的替代打开了一扇大门。

在接下来的部分中，我们将介绍gcForest并报告实验，然后是相关的工作和结论。



>图1：梯级森林结构的图示。假设级联的每个级别由两个随机森林（黑色）和两个完全随机的树林（蓝色）组成。 假设有三个类可以预测; 因此，每个森林将输出一个三维类向量，然后将其连接起来以重新表示原始输入。

> 图2：类向量生成的图示。叶节点中的不同标记意味着不同的类。

> 图3：使用滑动窗口扫描的特征重新表示的图示。 假设有三个类，原始特征是400-dim，滑动窗口是100-dim。

> 图4：gcForest的整个过程。 假设有三个类要预测，原始特征是400维，使用了三种滑动窗口的尺寸

### 2 拟议方法

在本节中，我们将首先介绍级联森林结构，然后是多粒度扫描，然后是整体架构和超参数的注释。

##### 2.1级联森林结构(Cascade Forest)

深度神经网络中的表征学习主要依赖于原始特征的逐层处理。受到这种认可机制的启发，gcForest采用级联结构，如图1所示，其中每级级联接收由其前一级处理的特征信息，并将其处理结果输出到下一级。

每个级别都是决策树森林的集合，即对一些集成的集成。在这里，我们包括不同类型的森林以鼓励多样性，众所周知，多样性对于整体建设至关重要[周，2012]。 为简单起见，假设我们使用两个完全随机的树森林（tree forests）和两个随机森林[Breiman，2001]。每个完全随机的树林包含500个完全随机的树[Liu et al。，2008]，通过随机选择一个特征生成 在树的每个节点分裂，并且不断增长树，直到每个叶节点只包含相同类的实例。 同样，每个随机森林包含500棵树，通过随机选择√d个特征作为候选（d是输入特征的数量）并选择一个分裂的最佳基尼值[基尼系数](https://zh.wikipedia.org/zh/%E5%9F%BA%E5%B0%BC%E7%B3%BB%E6%95%B0)。每个森林中树的数量是一个超参数，将在2.3节中讨论。

给定一个实例，每个森林将通过计算有关实例落在的叶节点上不同类别的训练样例的百分比，然后对同一森林中的所有树进行平均，来生成类分布的估计，如图所示图2，其中红色高亮显示实例遍历(traverses to )叶子节点的路径。

估计的类分布形成一个类向量，然后将其与原始特征向量连接起来，以便输入到下一级联。例如，假设有三个类，那么四个森林中的每一个都将产生一个三维类向量;因此，下一级级联将获得12（= 3×4）个增强特征。

为了降低过拟合的风险，每个森林产生的类向量通过`k-fold交叉验证`生成。详细地说，每个实例将用作训练数据k-1次，产生k-1类向量，然后将其平均以产生最终类向量作为下一级级联的增强特征。在扩展新级别之后，将在验证集上估计整个级联的性能，并且如果没有显着的性能增益，则训练过程将终止;因此，自动确定级联`级别`的数量。与模型复杂性固定的大多数深度神经网络相比，gcForest通过在适当时终止训练来自适应地决定其模型复杂性。这使其能够适用于不同规模的训练数据，而不仅限于大规模训练数据。

>*K*-fold cross-validation
>
>K次交叉验证，将训练集分割成K个子样本，一个单独的子样本被保留作为验证模型的数据，其他K-1个样本用来训练。交叉验证重复K次，每个子样本验证一次，平均K次的结果或者使用其它结合方式，最终得到一个单一估测。这个方法的优势在于，同时重复运用随机产生的子样本进行训练和验证，每次的结果验证一次，10次交叉验证是最常用的。

##### 2.2多粒度扫描

深度神经网络在处理特征关系方面具有强大的功能，例如，卷积神经网络(CNN)对图像数据有效，其中原始像素之间的空间关系是关键的[LeCun et al。，1998; Krizhenvsky等，2012];[递归神经网络](https://zh.wikipedia.org/wiki/%E9%80%92%E5%BD%92%E7%A5%9E%E7%BB%8F%E7%BD%91%E7%BB%9C)(RNN)对序列数据有效，其中序列关系是关键的[Graves et al。，2013; Cho等，2014]。受到这种认识的启发，我们通过多粒度扫描程序增强了几连森林。

如图3所示，滑动窗口用于扫描原始特征。假设有400个原始特征，并且使用了100个特征的窗口大小。对于序列数据，将通过滑动一个特征的窗口来生成100维特征向量;总共产生301个特征向量。如果原始特征具有空间关系，比如图像像素为400的20×20的面板，则一个10×10窗口将产生121个特征向量（即121个10×10的面板）。从正/负训练样例中提取的所有特征向量被视为正/负实例；它们将被用于生成类向量，如第2.1节：从相同大小的窗口提取的实例将用于训练完全随机树森林和随机森林，然后生成类向量并连接为变换特征。如图3所示，假设有3个类，并使用100维窗口;然后，每个森林产生301个三维类向量，导致对应于原始400维原始特征向量出现了1,806维变换特征向量。请注意，当转换的特征向量太长而无法容纳时，可以执行特征采样，例如，通过对滑动窗口扫描生成的实例进行子采样，因为完全随机树不依赖于特征分割选择，而随机森林对不准确的特征分割选择非常不敏感。

图3仅显示了一种尺寸的滑动窗口。 通过使用多个尺寸的滑动窗口，将生成不同粒度的特征向量，如图4所示。

##### 2.3总体程序和超参数
图4总结了gcForest的整个过程。假设原始输入具有400个原始特征，并且三个窗口大小用于多粒度扫描。对于m个训练示例，具有100个特征的窗口将生成301×m个100维训练示例的数据集。这些数据将用于训练一个完全随机的树林和一个随机森林，每个森林包含500棵树。如果要预测三个类，将获得如第2.1节中所述的1,806维特征向量。然后，转换后的训练集将用于训练一级级联森林。

类似地，尺寸为200和300个特征的滑动窗口将分别为每个原始训练示例生成1,206维和606维特征向量。利用前一等级生成的类向量进行增强的变换特征向量将分别用于训练二级和三级级联森林。将重复此过程，直到验证性能收敛。换句话说，最终模型实际上是级联森林的级联，其中级联中的每个级别由多个级别（级联森林）组成，每个级别对应于单粒度扫描，如图4所示。注意，对于困难的任务如果计算资源允许，用户可以尝试多粒度。

给定一个测试实例，它将通过多粒度扫描程序获得其相应的转换特征表示，然后通过级联直到最后一级。 最终预测将通过在最后一级聚合四个三维类向量，并使用具有最大聚合值的类来获得。

表1总结了深度神经网络和gcForest的超参数，其中给出了我们实验中使用的默认值。

> 表1：超参数和默认设置的摘要。 粗体突出了具有相对较大影响的超参数; “？”表示默认值未知，或通常需要针对不同任务的不同设置。

### 3 实验

##### 3.1配置
在本节中，我们将gcForest与深度神经网络和其他几种流行的学习算法进行比较。目标是验证gcForest可以实现与深度神经网络高度竞争的性能，甚至可以在各种任务中更轻松地进行参数调整。因此，在所有实验中，gcForest使用相同的级联结构：每个级别由4个完全随机的树森林和4个随机森林组成，每个森林包含500棵树，如第2.1节所述。三折交叉验证用于类向量生成。级联级别的数量是自动确定的。详细地说，我们将训练集分为两部分，即增长集和估计集（一些实验数据集与训练/验证集一起给出。 为避免混淆，我们将训练集生成的子集称为增长/估计集。）;然后我们使用增长集用于构建级联森林，并使用估计集来估计性能。如果增加新极联不会改善性能，则级联的增长终止并且获得估计的级别数。然后，基于合并增长和估计集来重新训练级联。对于所有实验，我们将80％的训练数据用于增长集，20％用于估计集。对于多粒度扫描，使用三种窗口大小。对于d（指特征维度）原始特征，我们使用尺寸为⌊d/16⌋，⌊d/8⌋，⌊d/4⌋的特征窗口;如果原始特征是面板结构（例如图像），则特征窗口也具有面板结构，如图3所示。注意，仔细的特定任务的调整可能可以带来更好的性能;尽管如此，我们发现即使使用相同的参数设置而不进行微调，gcForest也能够在广泛的任务中实现出色的性能。

对于深度神经网络配置，我们使用ReLU作为激活函数，使用交叉熵进行损失函数，adadelta用于优化，根据训练数据的规模，隐藏层的丢失率为0.25或0.5。 然而，网络结构超参数无法在任务中修复，否则性能将令人尴尬地令人不满意。 例如，网络在ADULT数据集上达到80％的准确度，在具有相同架构的YEAST上仅获得30％的准确度（仅改变输入/输出节点的数量以适应数据）。 因此，对于深度神经网络，我们检查验证集上的各种体系结构，并选择具有最佳性能的体系结构，然后在训练集上重新训练整个网络并报告测试准确性。

##### 3.2结果
###### 图像分类
MNIST数据集[LeCun等，1998]包含60,000个大小为28乘28的图像用于训练（和验证），以及10,000个图像用于测试。我们将其与LeNet-5（具有丢失和Re-LU的LeNet的现代版本），具有rbf内核的SVM以及具有2,000棵树的标准随机森林的重新实现进行比较。我们还包括[Hinton等，2006]中报道的深入网络的结果。测试结果表明，gcForest虽然只是使用表1中的默认设置，但却实现了极具竞争力的性能。
表2：MNIST测试精度的比较
人脸识别
ORL数据集[Samaria和Harter，1994]包含400个来自40个人的400张灰度面部图像。我们将它与由2个转换层组成的CNN进行比较，其中32个特征映射为3×3内核，每个转换层后面都有一个2×2的最大合并层。一层128个隐藏单元的密集层与卷积层完全连接，最后

### 5 结论

通过认识到深度学习的关键在于表征学习和大型模型能力，本文试图将这些属性赋予树集合并提出gcForest方法。 与深度神经网络相比，gcForest在实验中实现了极具竞争力的性能。 更重要的是，gcForest具有更少的超参数，在我们的实验中，通过使用相同的参数设置，可以在各个域中获得出色的性能。 gcForest的[代码](http://lamda.nju.edu.cn/code_gcForest.ashx )。

建造深度森林还有其他可能性。 作为一项开创性的研究，我们只是朝这个方向探讨了一点。 为了解决复杂的任务，学习模型可能需要深入研究。 然而，当前的深度模型总是神经网络。 本文阐述了如何构建深度森林，我们相信它可能为许多任务的深层神经网络的替代打开了一扇大门。