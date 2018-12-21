---
title: CMM/CMMI--OUT！
date: 2018-12-04 10:04:45
tags:
- 软件工程管理
---

#### 摘要

​	近几年，伴随着中国软件行业的高速发展和国家支持力度的不断加大，CMM/CMMI体系在IT行业日益流行并逐渐深入，许多企业高层将CMMI视为解决问题的灵丹妙药，各大小公司迫不及待的引入了CMMI咨询和认证，并不断向高级别冲刺，认为这样就可以解决长期困扰企业的项目延期、项目成本超标、项目质量低下等难题。但经过几年的工作，不少企业却不得不无奈的面对这样的现实：CMMI体系的引入带给企业的只是一些从来落实不下去的开发流程和文档模板、或者CMMI体系执行下去了，但效果却只是使研发文档齐全了一些、研发过程规范了一些，可是产品质量和开发进度却没有什么改进，甚至有所退步。

​	于是，质疑CMMI的声音大了起来，那么CMM/CMMI为什么不能在现在的开发中担任一个重要的过程改进角色呢？本文希望从几个不同角度入手探究CMM/CMMI体系不适合在当前软件开发当中应用的原因。

​	本文的文章结构分为三个部分，引言部分简单介绍了CMM/CMMI体系的来源和特点，然后从四个角度描述了CMM/CMMI不适合在当前软件开发当中应用的原因，最后讲述了现如今的企业要如何基于CMM/CMMI体系进行改进才能更好的适应当前开发环境。

#### abstract 

​	In recent years, along with the rapid development of China's software industry and the increasing support of the state, the CMM/CMMI system has become increasingly popular and in-depth in the IT industry. Many corporate executives regard CMMI as a panacea for solving problems, and companies of all sizes can't wait. The introduction of CMMI consulting and certification, and continue to sprint to a high level, that this can solve the long-term problems of the company's project delays, project costs exceeded, project quality and other issues. However, after several years of work, many companies have to face the reality: the introduction of the CMMI system brings only some development processes and document templates that have never been implemented, or the CMMI system has been implemented, but The effect is only to make the R&D documentation complete and the R&D process standardized, but the product quality and development progress have not improved, and even declined. 

​	So, questioning the voice of CMMI is so big, why can't CMM/CMMI play an important process improvement role in the current development? This article hopes to explore the reasons why the CMM/CMMI system is not suitable for application in current software development from several different perspectives. 

​	The structure of this article is divided into three parts. The introduction part briefly introduces the source and characteristics of CMM/CMMI system, and then describes the reasons why CMM/CMMI is not suitable for application in current software development from four angles. Finally, it tells the present How to improve the CMM/CMMI system can better adapt to the current development environment. 

#### 引言

​	1984年，美国国防部在其软件外包招标时，因无法有效评估应标软件公司/组织的开发管理能力，故委托美国卡内基美隆大学的软件工程学院研发出一整套行业标准体系**CMM**（软件能力成熟度模型，Capability Maturity Mode） ，以评估并改善软件开发公司/组织的软件开发过程及软件开发能力，从此，CMM逐渐成为了软件行业的质量管理和质量保证标准，随着CMM在业内的迅速普及，又陆续演进出不同专业领域的CMM模型，包括：软件能力成熟度模型 (Software Capability Maturity Model, SW-CMM) 、系统工程能力成熟度模型 (Systems Engineering Capability Maturity Model, SE-CMM) 、集成产品开发能力成熟度模型 (Integrated Product Development Capability Maturity Model, IPD-CMM) 、人力资源管理能力成熟度模型 (People Capability Maturity Model, P-CMM) 等诸多应用模型。基于上述各类CMM模型，SEI于2000年12月公布Capability Maturity Model - Integrated, 即**能力成熟度集成模型**CMMI，将上述各能力成熟度模型进一步优化与整合，并取代原CMM标准。

​	CMMI是CMM成功发展后的新修订版本，旨在开发一个通用的集成架构，以整合不同专业领域的特定能力成熟度模型及相关产品，并致力提供系统工程及软件工程的指导原则，期望通过CMMI的实施，推动任何架构下的组织改善其开发与管理流程。CMMI不仅提高了各能力级成熟度的要求门坎，也同时扩大了能力成熟度评估适用范围，使得软件工程、系统工程等专业领域及集成性产品与流程开发环境，都能运用CMMI为其提供持续的能力改进指导，对软件生产力与质量的提升有着显著的实质效益。

​	CMM/CMMI经过实践，在改进软件过程，协助软件开发人员提升研发能力，提升软件开发管理能力方面有着很好的效果，已经成为全球公认的有助于流程改善的机制。但是，伴随着互联网行业的发展，伴随着敏捷方法、精益思想、kanban等方法的流行，CMM/CMMI体系在企业中用的越来越少了，下面我们就几个方面思考一下CMM/CMMI体系在当前软件开发环境中不适合的原因。

#### CMM/CMMI不适合在当前软件开发当中应用的原因

##### 快速变化的市场需求

现在的软件开发环境中，大部分互联网相关的企业面对的是这样的需求：

- 产品的开发受市场影响较大，业务需求变更快，不断有新的需求加入。
- 团队之间沟通协作效率低，团队与客户之间沟通效率更低。
- 项目的开发风险比较高。 
- 盈利模式从产品的销售到依靠服务（提供个性化定制等）

再来看看哪些情况的公司会使用CMM/CMMI体系？下面这两类排在前面：

- 搞外包业务的企业，这些企业在招标或者与客户面谈中通常需要某些资质来证明自己的外包能力，而CMMI是国际上认证的能力证明。
- 通讯、电子等领域的研发型企业，这类企业研发的产品就是复杂产品线的产品，产品投入大，开发周期长，参与人数多。产品的复杂度决定了需要依靠CMMI这类重型流程来保证产品质量。



​	CMM/CMMI体系本身是一种非常重型的流程，它讲究的一个是稳定，一个是重复，如何稳定和高效的重复，并逐渐精进是CMM/CMMI体系所做的事情，所以这些开发复杂产品的公司需要它来进行过程的控制，但是现如今的大部分IT企业都没有这两类需求，这些互联网企业提供的大多数是一种服务，他们需要快速应对客户的需求变化，每一个迭代的开发周期也比较短，如果使用CMM/CMMI体系，繁重的文档和过程控制会拖慢开发的周期，带来的回报也不是那么明显，所以大部分互联网企业都不采用CMMI流程体系而是适用更加敏捷的过程改进方法。

##### 快速迭代的软件开发生命周期

​	从开发模型的角度来讲，CMMI的开发模型，一般以瀑布为主，也有螺旋和快速原型等经典的软件工程开发模式。而现在更普适的开发模型基本都是迭代，迭代周期一般从1周到1个月。CMM/CMMI更注重过程和文档，而当前开发环境要求我们更注重代码本身和团队的沟通协作。

​	从开发过程中的工具方面来说，CMMI有同行评审，而我们希望这个评审过程更简洁而迅速，所以我们推崇结对编程。CMMI有版本配置管理，而我们希望我们的代码可以快速的部署和更新，所以提出了持续集成的概念。CMMI有版本发布里程碑，而我们希望我们每一个变化都可以交付可使用的产品，所以我们期望持续交付。

​	上面讲述的软件开发生命周期的变化可以说明，在现在开发环境下，由于需求的快速变更，版本更新换代变快，软件团队必须快速响应这样的变化，CMMI这种大规模的分工协作反倒会带来一些麻烦，这种按部就班的追求项目按时完成的模式，不完全适应于现在软件开发更高的要求—为用户提供更好的服务，所以现在大部分公司都是大公司小团队精准化开发的模式，这样的开发生命周期才更适应市场的变化。

##### 创新阶段的产品和企业不适合用CMMI来管理

　　近年来，中国初创企业数量超过161万家，自2010年来每年以将近100%的速度增长，排在全球第一。创业公司因为人少，沟通成本降低，那怎样的开发流程可以提高效率的同时又能有条不紊呢？

​	CMMI诞生于传统软件开发场景下，改动和变化的成本高昂。所以花大量时间做计划，减少未知量，限制变化，希望有更多控制和预测。但是在瞬息万变的互联网产品永远在线永远交付的背景下，提前计划只会令人沮丧：项目会延迟，预算会超支，需求和政策都可能朝令夕改，因为每个项目在试图解决问题的同时都可能会带来新的未知问题的发现。更好的方法属于敏捷、看板、AB测试。

​	创新是需要打破惯性思维，不断开创出新市场、新产品、新技术和新方法。从产品管理的生命周期可以看到，产品从需求、设计、研发测试、发布部署、维护各过程中，大部分的阶段都不是稳定的，市场的多变导致了需求和设计方面的快速变化，在实现中，技术方案的创新，工艺方法，管理方法的创新等仍然有大量的不确定性，所以需要采用开放式的更为灵活的管理模式，以确保我们做出来的东西是客户想要的东西。

​	CMMI模型的精髓，是如何稳定和高效的重复，并逐渐精进。而互联网公司一般是创新导向，风格上都是大开大合，研发过程既不强调稳定，也不强调重复，所以并不适合用CMMI模型。

##### 理想VS实际

​	中国现在处在全民互联网时代，中小型企业铺天盖地，但真正能达到CMMI2级别以上的不多，很多企业是达到了CMMI3级别文档的要求，但实际项目的开发过程没有产生CMMI3级所预期的效果。在人员较少的中小型软件企业中，应用CMMI模型时会遇到很多困难，即使对于大型软件企业来言，其中的小部门和小项目在单独应用这些模型时也会有问题，这也是CMM/CMMI体系在现有企业中难以实施的一个原因。

​	实际上，CMMI达到3及以上的团队基本上有很好的产品管理能力和产品开发能力。所以，CMMI不是不好，而是不适合当前的软件开发环境，难以实施+作用微乎其微。

#### 如何让CMMI发挥更好的效果？

![v2-73435e015254ea28a92c40a0f7979909_hd](/Users/sweets/Desktop/v2-73435e015254ea28a92c40a0f7979909_hd.png)

　　上图描述了一个软件项目的复杂度随着需求和技术的改变而改变，需求明确，技术明确的简单项目，用传统模式管理，使用CMMI过程改进体系，成熟、而且效率更高，而部分复杂和复杂的可以使用敏捷，拥抱变更，不断改进，会比使用传统项目管理更灵活一些，变更成本和风险都会降低。

​	不管是使用CMMI还是其他管理方法，在现在的实际项目开发中，都需要掌握流程适度的原则：具体实施过程中需要理解CMMI中的要求，注意达到最终的效果而不是形式和步骤;例如：CMMI约定了设计方案需要评审，但评审方式可以灵活，可以邀请客户参加，也可以与外部技术专家讨论，甚至于我们不需要开正式的会议;评审议程不一定按照既定的模式，而是根据技术方案的明确程度来选择。这样看来，在制定标准流程时注意约定主要的活动和想要达成的效果，而不是详细的步骤，只有这样才能保证最终流程的有效性和灵活性。当然，CMMI在成熟度较高的一定情况下可以结合敏捷、精益等方法，对软件开发过程会有更好的效果。

​	总之，企业项目开发过程采用什么模型应该根据自身情况决定，并且不应该硬搬模型，应根据自身情况对其进行裁剪使其适应公司使用。不管传统软件开发方法，精益，还是敏捷，我们都应该始终记得我们的最终目标：为用户提供更好的服务。按时完成任务只是我们达到这个目标的手段。

#### 参考文献

1. Minna Pikkarainen《Towards a Framework for Improving Software Development Process Mediated with CMMI Goals and Agile Practices》
2. [什么是CMM和CMMI](http://www.qaichina.com/html/2012-07-20/page_6290.html)
3. [创业团队快速迭代中的三件工具](https://zhuanlan.zhihu.com/p/26073435)





















