---
title: "IAM：从基本词汇入手，了解一人多户的问题"
date: 2020-04-11T15:18:05+08:00
draft: false
---

最近写的少了，是因为一直在啃源码。啃源码的过程是痛苦的，而且是不被认可的。前两天听了一个网上讲座，说是把开源（国外或者国内）的东西拿过来研究和应用，是农民企业家。没想到苦读寒窗十余年加上辛苦劳作，还是没有脱离农民的身份。

不过，想想自己劳作的成果为几万人提供了服务，想想自己劳作的成果简化了几万人的操作，就如同看着金灿灿的玉米，满脸幸福感，也很知足了。

接下来的咬文嚼字，主要是理清用户身份治理中的基本概念。为此我查了[剑桥英语词典来进行准确释义](https://dictionary.cambridge.org/zhs/%E8%AF%8D%E5%85%B8/%E8%8B%B1%E8%AF%AD/)。

## 用户（User）

someone who uses a product, machine, or service

从词义上看，用户主要是从产品（或服务）的使用权方面来讲的。

## 身份（Identity）

who a person is, or the qualities of a person or group that make them different from others

这是个哲学问题，“你是谁”就是问你的身份，不是问你是人还是你有钱否，而是实实在在地问你有别于他人的质素。

## 帐户（Account）

an agreement with a company that allows you to use the internet and email, and gives you space on the internet to put your documents, etc.

帐户是你（用户）与公司（组织）之间的一份约定，允许你访问组织所拥有的某些资源。这个某些，就是你和组织之间的约定。

## 用户名（Username）

a name or other word that you sometimes need to type in along with a password before you are allowed to use a computer or a website.

帐户是约定，能够让你登录进入系统，你必须需要一个在系统中唯一且与你关联的用户名。

## 组（Group）

a number of people or things that are put together or considered as a unit.

在之前的文章里提过，组主要是指用户因为某种关系聚在一起，形成一个统一的团体进行操作。

## 角色（Role）

the duty or use that someone or something usually has or is expected to have.

角色是从其所承担的责任，所拥有的权责来讲的。拥有这个角色，就拥有该角色的权责。

## 一人多户

在高校中，经常存在一种情况，一个人从本科生读到研究生，甚至于既是博士生又是在校在编教职工。在每一阶段都会有代表其身份的帐户存在。这对信息化工作的影响并不大。影响较大的是从学生到临时人员又到在编人员的场景。这种场景下，其不同的帐户代表不同的身份。这该如何处理呢？

我觉得以上几个概念清楚之后，也必然清晰地知道了该如何对待一人多户的问题。重要的问题，在于其核心用途是什么，核心用途决定了解决方案。
