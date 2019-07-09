---
title: Experimental development based on mapping rule between requirements analysis model and web framework specific design model
---

### 原作者
* Hirotaka Okuda
* Shinpei Ogata
* Saeko Matsuura

### 原文链接
https://springerplus.springeropen.com/articles/10.1186/2193-1801-2-123

##### 摘要
模型驱动开发是开发出高质量软件系统的一种可靠的方式。我们已经提议一种利用Unified Modeling Language(UML)来作为模型驱动的需求分析方式。我们的方法的主要特性是通过UML需求分析模型来自动生成一个web用户接口原型，以此我们可以确定我们输入输出数据的有效性以及通过直接操作原型确定页面之间的转换。我们提议在基于需求分析模型的基础上，对不同的web应用框架实现上有一个映射规则，也为了提高从一个有效需求分析模型到最终产品的可追踪性。这篇论文讨论了通过此方式开发出的，最近工作在我们公寓的团队工作支持系统。

##### 关键词 
web frameworks, unified modeling language, requirements analysis model

##### 介绍
当今许多有用的服务和产品像是计算机系统需要开发者有效地开发一个高质量的系统，所以他们可以迎合不同的知识背景的用户，不同的使用情况，不同的输入输出设备像是智能电话和个人计算机。为了确保这样的一种开发，软件在整个生命周期的可追踪性是很重要的，而且模型驱动开发方式是一种可靠的方式。

我们已经提议一种使用UML的基于模型驱动的需求分析方法(Ogata & Matsuura 2008; 2010a)。我们方法的主要的特性是通过UML需求分析模型来自动生成web用户接口原型，所以我们通过直接操作原型，可以确保对每个页面和页面转换的输入输出数据的有效性。我们已经显示需求分析模型拥有可追踪性

