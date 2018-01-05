---
layout: article
title:  "如何在 Tableau 中利用 SQL Server 的 R 和 Python 功能？"
date:   2018-01-01 22:07:50 +0800
categories: infovis 
image:
  teaser: tableau article.png
  feature: tableau article.png
---

# 实战案例：如何在 Tableau 中利用 SQL Server 的 R 和 Python 功能？

> 原创 2018-01-02 Tableau Tableau社区  
文章来源  
Tableau 商业智能博客
作者：Bora Beran（Tableau 产品经理）
了解更多 Tableau 最新功能及应用技巧，欢迎登陆 Tableau 商业智能博客：
https://www.tableau.com/about/blog  （复制链接至浏览器可查看更多信息）

## 导语
我们习惯将 Tableau 视为 “数据中的瑞士” （瑞士和很多国家领土接壤，在这里比喻 Tableau 能链接各种数据源），因为它有着各种各样的数据源连接选项。但谁说连接到数据源意味着只能从事务数据库或数据仓库读取数据呢？ 如果您的数据库具有数据库内分析功能，则 Tableau 可以利用这些功能来增强分析功能。

Tableau 与 R，Python 和 MATLAB 的集成由于其硬件独立的性质而非常流行，但如果从这些接口得到的结果是具有任意数量的行和列的表格，或者如果您想要将结果保存在摘录中，那这就不是一个好的选择。

SQL Server 是 Tableau 用户中最受欢迎的数据库，在过去两年中也一直在构建其预测分析功能。SQL Server 2016 添加了支持 R 语言在数据库中运行的功能，SQL Server 2017 将其扩展到 Python 。在本文中，我将通过两个高级分析案例来说明您如何在 Tableau 中利用微软 SQL Server 的 R 和 Python 功能。
*** 
## 案例一：每日社交媒体数据分析

假设您希望分析每日社交媒体数据并在 Tableau 数据源中获取数据，以此来查看热门话题的言论走向。您可以将用于此分析的 Python 代码作为初始 SQL 插入到 Tableau 中的 SQL Server 数据源中。

![图一](https://mmbiz.qpic.cn/mmbiz_png/7VgAHPKib9QmHeQq8NJb3l2ulhicaiaHXZTYjgexAssMBCeMh7jOAlt2sSw8jH37BMlvBtE58QutfP4OCZQ306mBg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)

> 这是初始 SQL 对话框屏幕截图，显示了代码摘录  （我已经将我的帐户信息盖上水印了）


这个示例依赖于 SQL Server 与 Python 的集成，从 Twitter 检索一天内带有 #Machine Learning 标签的有效数据，然后利用 SQL Server 来运行 Microsoft 提供的情感分析模型。要将数据导入 Tableau，我们需要使用用户自定义的 SQL 语句，从初始 SQL 生成的临时表格中检索结果。

![image](http://mmbiz.qpic.cn/mmbiz_png/7VgAHPKib9QmHeQq8NJb3l2ulhicaiaHXZTc8KtxYzPiaibvzJjeH7yIGjyPSVEZqZzR7QBKcUC9Sp8eEuvbcq1SkSQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)

结果将是一个返回到 Tableau 的表格，其中包含原始推文，推特 ID ，用户句柄，日期时间，关联的情感评分和情感分类。

![image](https://mmbiz.qpic.cn/mmbiz_png/7VgAHPKib9QmHeQq8NJb3l2ulhicaiaHXZTL7fH8Iicdia9gbdCbEvxWzRmGXW4JUXQWkfOh70LREcPr4nicREKu4PPw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)

下一步是按时间表运行报告，以便只有新数据被附加到历史数据。 您可以通过使用增量提取并刷新时间表的方法，将此数据源发布到服务器来实现，如下面的屏幕截图所示。

![image](https://mmbiz.qpic.cn/mmbiz_png/7VgAHPKib9QmHeQq8NJb3l2ulhicaiaHXZTOJRSfUWrGhLTpBKSLJFRAibzZTZ3NSGJZAroqN2miaedBQeyLd5OEv9Q/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)

我可以使用此数据源快速构建可视化，以便随时查看情绪变化，并可以每天自动添加新的数据和分析结果。

![image](https://mmbiz.qpic.cn/mmbiz_png/7VgAHPKib9QmHeQq8NJb3l2ulhicaiaHXZT9mo4yuBQricZLRt6j1Siccib0yialIOODYaKtO5RqqQEQe9icr9lyKk62ow/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)

看起来对于机器学习相关话题的情感是正面的。
> ++复制下方链接至浏览器下载此示例中使用的初始 SQL 脚本。
https://www.tableau.com/sites/default/files/blog/sql.txt++

为了保证您可以进行这项工作，请确保您已安装 SQL Server 机器学习服务和预训练模型，启用外部脚本执行，安装必要的 R （预测）和 Python （Twitter）软件包，并且设置 “网络可以访问 R 本地用户帐户” 使其不会阻止您的数据库。

在第一个示例中，我能够将代码直接插入到 Tableau 中，这对于临时使用来说是一个很好的方法。然而，企业通常希望在生产中使用预测模型，并且倾向于将它们作为标准化方法来展示 - 在数据库的情况下通常意味着使用存储过程。
***
## 案例二：使用 R 创建存储过程

让我们在下一个例子中使用 R 创建一个带有输入参数的存储过程。 这样，用户通过 Tableau 中相应的可视化指定标准来定制输出，如预测的长度和预测带的范围。

我可以使用 SQL Server Management Studio 创建我的存储过程，如下图所示。

![image](https://mmbiz.qpic.cn/mmbiz_png/7VgAHPKib9QmHeQq8NJb3l2ulhicaiaHXZTgThaxlic4yHcOYVG7rewRgrzGCDV4qgWKp99lwXPAqkklLYIkJq3AEQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)

> ++复制下方链接至浏览器下载这个例子的 SQL 脚本。
https://www.tableau.com/sites/default/files/blog/forecastsql_0.txt++

完成此操作，连接到数据库后，我可以在 Tableau 的数据准备选项卡中看到我的存储过程。在将其拖入我的连接图并分配输入参数之后，我已准备好在可视化中使用它。下面的屏幕截图显示了 Tableau 中的存储过程配置。

![image](http://mmbiz.qpic.cn/mmbiz_png/7VgAHPKib9QmHeQq8NJb3l2ulhicaiaHXZTDMSrAyuWJ3HCSk9U1yttv0EhyYtakrJfLUe0NYT3ib9SHTlZibQ0TY9A/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)

现在我可以调整参数，并立即从数据库中获得新的预测。

![image](https://mmbiz.qpic.cn/mmbiz_gif/7VgAHPKib9QmHeQq8NJb3l2ulhicaiaHXZTibqfVmFymKH2KHw3coFAGGj1eHTWr3rYib3vhuJFzyBiclasmjfId3lew/0?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)

这是如何利用 Tableau 中 SQL Server 的 R 和 Python 集成的快速概览。由于这些语言提供的广泛的机器学习库，所以这将有无限的可能性。我们期待着看到您会想出的绝妙解决方案！
