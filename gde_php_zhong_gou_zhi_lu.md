# G+的PHP重构之路

重构入门: 

1. 确定命名: 方法和类用驼峰,变量_
2. 尽可能的消除重复代码

现在主要的类型有:

1. activity
2. applyForm
3. crowdfunding
4. questionaire

## 1. release_pre

这是进入发布页面前需要获取一些用户的信息,用来填充一些控件

首先查找这几个重复度好大,但是只有这几部分不同

以activity为基础

1. 共同不同,跳转的url和menu不一致
2. activity比applyform多新手指导、获取地址、获取社群的基本信息
3. activity和crowdfunding基本一致
4. activity比questionnaire多一个新手指导