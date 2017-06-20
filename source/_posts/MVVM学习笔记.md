---
title: MVVM学习笔记
date: 2017-05-20 11:38:51
categories: 笔记
tags: [iOS,MVVM]
---

## MVVM的四个组件：
* view ：由 MVC 中的 view 和 controller 组成，负责 UI 的展示，绑定 viewModel 中的属性，触发 viewModel 中的命令；
* viewModel ：从 MVC 的 controller 中抽取出来的展示逻辑，负责从 model 中获取 view 所需的数据，转换成 view 可以展示的数据，并暴露公开的属性和命令供 view 进行绑定；
* model ：与 MVC 中的 model 一致，包括数据模型、访问数据库的操作和网络请求等；
* binder ：在 MVVM 中，声明式的数据和命令绑定是一个隐含的约定，它可以让开发者非常方便地实现 view 和 viewModel 的同步，避免编写大量繁杂的样板化代码。在微软的 MVVM 实现中，使用的是一种被称为 XAML 的标记语言。
<!--more-->
总结：在 iOS 的 MVVM 实现中，我们可以使用 RAC 来在 view 和 viewModel 之间充当 binder 的角色，优雅地实现两者之间的同步。此外，我们还可以把 RAC 用在 model 层，使用 Signal 来代表异步的数据获取操作，比如读取文件、访问数据库和网络请求等。

## 实现MVC到MVVM的转变
> 将 MVC 中的 controller 中的展示逻辑抽取出来，放置到 viewModel 中，然后通过一定的技术手段，比如 RAC 来同步 view 和 viewModel。

## MVVM相较于MVM的优势
* 由于展示逻辑被抽取到了 viewModel 中，所以 view 中的代码将会变得非常轻量级；
* 由于 viewModel 中的代码是与 UI 无关的，所以它具有良好的可测试性；
* 对于一个封装了大量业务逻辑的 model 来说，改变它可能会比较困难，并且存在一定的风险。在这种场景下，viewModel 可以作为 model 的适配器使用，从而避免对 model 进行较大的改动。

参考链接：[MVVM With ReactiveCocoa](http://blog.leichunfeng.com/blog/2016/02/27/mvvm-with-reactivecocoa/)
