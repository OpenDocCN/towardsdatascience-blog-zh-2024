# 实践中的合成数据：Shopify案例研究  

> 原文：[https://towardsdatascience.com/synthetic-data-in-practice-a-shopify-case-study-79b0af024880?source=collection_archive---------6-----------------------#2024-12-10](https://towardsdatascience.com/synthetic-data-in-practice-a-shopify-case-study-79b0af024880?source=collection_archive---------6-----------------------#2024-12-10)

## 使用30k条记录数据集测试新的Snowflake功能  

[](https://medium.com/@piotr.gruszecki_22364?source=post_page---byline--79b0af024880--------------------------------)[![Piotr Gruszecki](../Images/0d8b89957a88b258e87887cf7e1de093.png)](https://medium.com/@piotr.gruszecki_22364?source=post_page---byline--79b0af024880--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--79b0af024880--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--79b0af024880--------------------------------) [Piotr Gruszecki](https://medium.com/@piotr.gruszecki_22364?source=post_page---byline--79b0af024880--------------------------------)  

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--79b0af024880--------------------------------) ·13分钟阅读·2024年12月10日  

--  

![](../Images/86f740f9908c2f0378251c2b79b24a67.png)  

由DALL·E根据作者的提示生成的图像  

在处理数据时，我越来越频繁地遇到同样的问题。一方面，我们对数据隐私和保密性的要求日益增加；另一方面——我们需要快速做出数据驱动的决策。再加上现代商业的现实：自由职业者、顾问和短期项目。  

作为决策者，我面临一个两难：我现在需要分析，内部团队已经超负荷工作，而且我不能随便把机密数据交给每一个外部分析师。  

这就是合成数据的作用所在。  

等等——我不想再写一篇关于合成数据是什么的理论文章了，网上已经有足够多了。相反，我将向你展示一个具体的对比：30,000笔真实的Shopify交易与它们的合成数据对比。  

我到底检查了什么？  

+   合成数据能多忠实地反映真实趋势？  

+   最大的差异在哪里？  

+   我们什么时候可以信任合成数据？又在什么情况下应该谨慎？  

这不会是另一篇“如何生成合成数据”的指南（虽然我也会展示代码）。我关注的是真正重要的——这些数据是否真正有用，它的局限性是什么。  

我是一个实战派——少说理论，更多具体细节。让我们开始吧。  

# 数据概览  

在测试合成数据时，你需要一个坚实的参考点。在我们的案例中，我们使用的是来自一家成长中的电子商务企业的真实交易数据：  

+   30,000笔交易，跨越6年  

+   年年增长趋势明显  

+   销售月份的高低波动混合  

+   多样化的地理分布，其中一个市场占主导地位  

![](../Images/31ef72599e5edf82fdb5519351e97c5c.png)

所有图表由作者创建，使用他自己的R代码

对于实际测试，我专注于交易级数据，如订单金额、日期和基本地理信息。大多数评估只需要基本的业务信息，不涉及个人或产品的详细信息。

该过程很简单：导出原始Shopify数据，分析后仅保留最重要的信息，在Snowflake中生成合成数据，然后将这两个数据集并排比较。可以将其视为生成“数字双胞胎”业务数据，具有可比的趋势，但完全匿名化。

*[技术说明：如果你对详细的数据准备过程感兴趣，包括R代码和Snowflake设置，请查看本文末尾的附录。]*

# 月度收入分析

任何合成数据集的首要测试是它能多好地捕捉核心业务指标。让我们从月度收入开始——这无疑是任何业务中最重要的指标之一（肯定排在前三名）。

从原始趋势（图1）来看，两个数据集都遵循类似的模式：多年持续增长，并伴有季节性波动。合成数据很好地捕捉了总体趋势，包括业务的增长轨迹。然而，当我们深入探讨差异时，一些有趣的模式浮现出来。

为了量化这些差异，我计算了一个月度变化值：

```py
Δ % = (Synthetic - Shopify) / Shopify
```

![](../Images/b90f2b2c5623a7444841831b96c2d95f.png)

从图中可以看到，月度收入变化值是有波动的——有时原始数据更大，有时是合成数据更大。但这些柱状图看起来是对称的，而且随着时间的推移，差异变得越来越小。我增加了每月记录数（交易量），也许它有一些影响？让我们深入探讨一下。

这些变化值确实相当平衡，如果我们看累计收入曲线，它们非常吻合，没有大的波动。我跳过这个图表。

# 样本量的影响

变化值变小了，我们直觉上觉得是由于记录数的增加。让我们检查一下——下一个图表显示了月度收入变化值的绝对值与每月记录数的关系。虽然记录数随着时间的推移在增长，但X轴实际上并非时间——而是记录数。

![](../Images/cfe8da970d0b893877a5aa979e66f96a.png)

这些变化值（绝对值）确实在减少，因为每月记录数增多——正如我们预期的那样。但还有一件事，相当有趣，而且不那么显而易见，至少在初次看来。每月记录数超过约500条后，变化值不再进一步下降，它们保持在（平均值）大致相同的水平。

尽管这个具体数字来源于我们的数据集，并可能因不同的商业类型或数据结构而有所变化，但模式本身是重要的：存在一个阈值，在这个阈值下，合成数据的稳定性显著提高。在这个阈值以下，我们看到较大的方差；而超过阈值后，差异稳定下来，但并未完全消失——合成数据根据设计保持一定的变化，这实际上有助于隐私保护。

存在噪声，这使得每月的数值变得随机化，尤其在样本较大时。所有这些都同时在较高的汇总（年度或累计）数据上保持一致性，并且能够很好地再现总体趋势。

如果能看到其他指标和数据集的类似图表，将会非常有趣。

我们已经知道收入增量依赖于记录数，但这仅仅是因为某个月份的记录数越多，合成数据的收入就越高吗？让我们来探讨一下…

因此，我们希望检查收入增量如何依赖于记录数增量。我们所说的增量是指Synthetic-Shopify之间的差异，无论是月收入还是月记录数。

下图展示了这种关系。存在一定的（轻微的）相关性——如果每月记录数在Synthetic和Shopify之间差异显著，或反之（高的增量值），收入的增量也会随之变化。但这远不是简单的线性关系——其中也存在额外的噪声。

![](../Images/765bdbe50a11b668f852fffec32616f3.png)

# 维度分析

在生成合成数据时，我们通常不仅需要保持整体指标，还需要保持它们在不同维度上的分布，比如地理分布。我在我们的测试数据集中保留了国家和州列，以便查看合成数据如何处理维度分析。

结果揭示了两个重要方面：

1.  合成数据的可靠性在很大程度上依赖于每个维度内的样本量

1.  维度之间的依赖关系未被保留

查看按国家划分的收入：

![](../Images/3ae2bbb8dc5959e1b5593f23ea6ee3d2.png)

对于有成千上万交易的主导市场，合成数据提供了可靠的表示——实际和合成数据集之间的收入总额是可以比较的。然而，对于交易较少的国家，差异变得显著。

关于维度关系的一个关键观察：在原始数据集中，州信息仅出现在美国交易中，其他国家的州信息为空。然而，在合成数据中，这种关系丧失——我们看到在国家和州列中随机生成的值，包括分配给其他国家的州，而非美国。这突显了一个重要的限制：合成数据生成并未保持维度之间的逻辑关系。

然而，确实有一种实用的方法可以克服这个国家-州依赖性问题。在生成合成数据之前，我们可以通过将国家和州合并为一个维度来预处理输入（例如，“US-California”，“US-New York”，而对于非美国交易，只保留“Germany”或“France”）。这一简单的预处理步骤将保留州是美国特有的业务逻辑，并防止在合成数据中生成无效的国家-州组合。

这有重要的实践意义：

+   合成数据在高流量细分市场中效果良好

+   在分析较小细分市场时要小心

+   在得出结论之前，务必检查样本量

+   要注意，维度之间的逻辑关系可能会丢失，可以考虑对某些列进行预聚合处理

+   如果维度完整性至关重要，考虑进行额外的数据验证

# 交易价值分布

这项分析中最有趣的发现之一来自于检查交易价值分布。逐年查看这些分布揭示了合成数据的优点和局限性。

![](../Images/81ea005b766e189066459f614c5fe583.png)

原始的Shopify数据展示了你在电子商务中通常会遇到的情况：高度不对称的分布，向更高值的长尾，并且有与畅销单品交易相对应的显著峰值，展现了明显的畅销产品模式。

合成数据讲述了一个有趣的故事：它很好地保持了分布的整体形状，但来自畅销产品的显著峰值被平滑化了。分布变得更“理论化”，失去了一些现实世界的特征。

这种平滑效应不一定是坏事。事实上，在某些情况下，它可能是更可取的：

+   对于一般的商业建模和预测

+   当你想避免过拟合于特定的产品模式时

+   如果你关注的是潜在的趋势，而不是具体的产品效果

然而，如果你特别关注畅销产品分析或单一产品交易模式，你需要考虑到合成数据的这个局限性。

知道我们的目标是产品分析，我们将会以不同的方式准备原始数据集。

为了量化合成数据与真实分布的匹配程度，我们将在下一节中讨论统计验证。

# 统计验证（K-S检验）

让我们通过科尔莫戈罗夫-斯米尔诺夫检验来验证我们的观察结果——这是一种用于比较两个分布的标准统计方法。

![](../Images/f4eb956f09eb0fd83d377d84b726b859.png)

这些发现是积极的，但这些数字在实践中意味着什么呢？科尔莫戈罗夫-斯米尔诺夫检验比较了两个分布并返回了两个重要的指标：D = 0.012201（越小越好，0表示分布完全相同），p值 = 0.0283（低于通常的0.05水平，表示统计上有显著差异）。

虽然p值显示分布之间存在一定的变化，但非常低的D统计量（接近0）验证了图表的发现：中间部分几乎完美匹配，只有在极端值处有轻微差异。合成数据捕捉了关键的模式，同时保持足够的方差以确保匿名性，使其适用于商业分析。

在实际操作中，这意味着：

+   合成数据在交易值的最重要中间范围内提供了非常匹配的结果

+   该匹配在我们拥有最多数据点的地方特别强

+   差异主要出现在边缘案例中，这是预期的，甚至从隐私角度来看是可取的

+   统计验证确认了我们从分布图中获得的视觉观察结果

这种统计验证在决定是否使用合成数据进行任何特定分析之前至关重要。在我们的案例中，结果表明合成数据集对于大多数商业分析用途是可靠的，尤其是在关注典型交易模式而非极端值时。

# 结论

让我们总结一下从真实的Shopify交易到其合成对等物的过程。

整体业务趋势和模式得以保持，包括交易价值分布。尖峰已被平滑，结果呈现出更加理论化的分布，同时保持关键特征。

样本大小很重要，这是有意设计的。过于细化会产生噪音，同时确保保密性（当然还会移除所有个人身份信息）。

列之间的依赖关系未被保留（如国家-州），但有一个简单的解决办法，因此我认为这不是一个真正的问题。

了解生成的数据集将如何使用非常重要——我们预期进行什么样的分析，以便在重塑原始数据集时考虑到这一点。

合成数据集将非常适用于应用程序测试，但我们应该手动检查边缘案例，因为这些在生成过程中可能被遗漏。

在我们的Shopify案例中，合成数据在大多数商业分析场景中证明是足够可靠的，尤其是在处理较大的样本并专注于一般模式时，而不是特定的产品级别分析。

# 未来工作

本次分析专注于交易，作为一个关键指标和一个易于入手的案例。

我们可以继续进行产品分析，也可以探索多表场景。

同样，值得制定内部指南，说明如何使用合成数据，包括检查和限制。

# 附录：数据准备和方法论

你可以浏览这一部分，因为它非常技术性，涉及如何准备数据。

## 原始数据导出

我没有依赖预先汇总的Shopify报告，而是直接使用了原始交易数据。在Alta Media，我们采用的是这种标准方法——我们更倾向于使用原始数据，以保持对分析过程的完全控制。

从Shopify导出的过程是直接的，但并非即时的：

+   从管理面板请求导出原始交易数据

+   等待包含下载链接的电子邮件

+   下载多个包含 CSV 数据的 ZIP 文件

## 数据重塑

我使用 R 进行探索性数据分析、处理和可视化。代码片段是 R 语言的，来自我的工作脚本，但当然也可以使用其他语言来实现相同的最终数据框。

初始数据集有几十列，所以第一步是选择与我们合成数据实验相关的列。

*代码格式已调整，以便我们避免出现水平滚动条。*

```py
#-- 0\. libs
pacman::p_load(data.table, stringr, digest)

#-- 1.1 load data; the csv files are what we get as a 
# full export from Shopify
xs1_dt <- fread(file = "shopify_raw/orders_export_1.csv")
xs2_dt <- fread(file = "shopify_raw/orders_export_2.csv")
xs3_dt <- fread(file = "shopify_raw/orders_export_3.csv")

#-- 1.2 check all columns, limit them to essential (for this analysis) 
# and bind into one data.table
xs1_dt |> colnames()
# there are 79 columns in full export, so we select a subset, 
# relevant for this analysis
sel_cols <- c(
"Name", "Email", "Paid at", "Fulfillment Status", "Accepts Marketing", 
"Currency", "Subtotal", 
"Lineitem quantity", "Lineitem name", "Lineitem price", "Lineitem sku", 
"Discount Amount", "Billing Province", "Billing Country")
```

我们需要一个数据框，所以我们需要将三个文件合并。由于我们使用的是 `data.table` 包，语法非常简单。然后我们将合并后的数据集通过管道传递，去除不需要的列，仅保留选定的列。

```py
xs_dt <- data.table::rbindlist(
  l = list(xs1_dt, xs2_dt, xs3_dt), 
  use.names = T, fill = T, idcol = T) %>% .[, ..sel_cols]
```

我们还将列名更改为单个字符串，用下划线“_”替换空格——这样我们就不需要在 SQL 中处理额外的引号。

```py
#-- 2\. data prep
#-- 2.1 replace spaces in column names, for easier handling
sel_cols_new <- sel_cols |> 
  stringr::str_replace(pattern = " ", replacement = "_")

setnames(xs_dt, old = sel_cols, new = sel_cols_new)
```

我还将交易 ID 从字符型“#1234”改为数值型“1234”。我创建了一个新列，这样我们就可以轻松比较转换是否按预期进行。

```py
xs_dt[, `:=` (Transaction_id = stringr::str_remove(Name, pattern = "#") |> 
              as.integer())]
```

当然，你也可以覆盖它。

## 额外的实验

由于这是一个关于 Snowflake 合成数据生成的实验，我进行了额外的准备工作。Shopify 导出包含了实际客户的电子邮件，而这些电子邮件在 Snowflake 中生成合成数据时会被屏蔽，但我还是对它们进行了哈希处理。

所以我使用 MD5 对这些电子邮件进行了哈希处理，并创建了一个包含数字哈希值的额外列。这纯粹是实验性的——我想看看 Snowflake 如何处理不同类型的唯一标识符。

默认情况下，Snowflake 会将基于文本的唯一标识符进行屏蔽，因为它将其视为个人可识别信息。对于实际应用，我们希望删除任何可能识别客户的数据。

```py
new_cols <- c("Email_hash", "e_number")
xs_dt[, (new_cols) := .(digest::digest(Email, algo = "md5"),
                        digest::digest2int(Email, seed = 0L)), .I]
```

我也很好奇逻辑列会如何处理，所以我更改了一个二进制列的类型，该列具有“yes/no”值。

```py
#-- 2.3 change Accepts_Marketing to logical column
xs_dt[, `:=` (Accepts_Marketing_lgcl = fcase(
    Accepts_Marketing == "yes", TRUE, 
    Accepts_Marketing == "no", FALSE, 
    default = NA))]
```

## 过滤交易记录

数据集包含每个项目的记录，而对于这项特定分析，我们只需要交易记录。

```py
xs_dt[Transaction_id == 31023, .SD, .SDcols = c(
  "Transaction_id", "Paid_at", "Currency", "Subtotal", "Discount_Amount", 
  "Lineitem_quantity", "Lineitem_price", "Billing_Country")]
```

![](../Images/505e4fd5854a0a53d702c5419b4b126a.png)

最终的列子集并过滤支付总额的记录

```py
trans_sel_cols <- c(
  "Transaction_id", "Email_hash", "e_number", "Paid_at", "Subtotal", 
  "Currency", "Billing_Province", "Billing_Country",
  "Fulfillment_Status", "Accepts_Marketing_lgcl")
xst_dt <- xs_dt[!is.na(Paid_at), ..trans_sel_cols]
```

## 导出数据集

一旦我们有了数据集，我们需要将其导出为 csv 文件。我导出了完整数据集，并且还生成了一个 5% 的样本，用于在 Snowflake 中的初步测试。

```py
#-- full dataset
xst_dt |> fwrite(file = "data/transactions_a.csv")
#-- a 5% sample
xst_5pct_dt <- xst_dt[sample(.N, .N * .05)]
xst_5pct_dt |> fwrite(file = "data/transactions_a_5pct.csv")
```

还将其保存在 Rds 格式中，这样我就不需要重复所有准备步骤（这些步骤是脚本化的，因此无论如何都会在几秒钟内执行）。

```py
#-- 3.3 save Rds file
list(xs_dt = xs_dt, xst_dt = xst_dt, xst_5pct_dt = xst_5pct_dt) |> 
  saveRDS(file = "data/xs_lst.Rds")
```

# 附录：在 Snowflake 中生成数据

一旦我们根据需求准备好数据集，生成其合成的“兄弟”数据集就变得简单了。需要上传数据、运行生成过程并导出结果。有关详细信息，请参考 Snowflake 指南。无论如何，我会在这里简要总结，以完整本文。

首先，我们需要做一些准备工作——角色、数据库和仓库。

```py
USE ROLE ACCOUNTADMIN;
CREATE OR REPLACE ROLE data_engineer;
CREATE OR REPLACE DATABASE syndata_db;
CREATE OR REPLACE WAREHOUSE syndata_wh WITH
  WAREHOUSE_SIZE = 'MEDIUM'
  WAREHOUSE_TYPE = 'SNOWPARK-OPTIMIZED';

GRANT OWNERSHIP ON DATABASE syndata_db TO ROLE data_engineer;
GRANT USAGE ON WAREHOUSE syndata_wh TO ROLE data_engineer;
GRANT ROLE data_engineer TO USER "PIOTR";
USE ROLE data_engineer;
```

如果尚未定义，则创建模式和阶段。

```py
CREATE SCHEMA syndata_db.experimental;

CREATE STAGE syn_upload 
 DIRECTORY = ( ENABLE = true ) 
 COMMENT = 'import files';
```

上传 csv 文件到阶段，然后将它们导入到表中。

然后，生成合成数据。我喜欢先做一个小的“试点”，比如5%的记录，用来初步检查是否可以通过。这是一个节省时间的办法（也节省成本），尤其是在更复杂的情况下，我们可能需要进行一些SQL调整。在这种情况下，它更多的是形式化操作。  

```py
-- generate synthetic
-- small file, 5% records
call snowflake.data_privacy.generate_synthetic_data({
    'datasets':[
    {
        'input_table':  'syndata_db.experimental.transactions_a_5pct',
        'output_table': 'syndata_db.experimental.transactions_a_5pct_synth'    
    }
    ],
    'replace_output_tables':TRUE
});
```

检查我们得到的结果是很有必要的——直接在Snowflake中检查表格。  

然后运行完整的数据集。  

```py
-- large file, all records
call snowflake.data_privacy.generate_synthetic_data({
    'datasets':[
    {
        'input_table':  'syndata_db.experimental.transactions_a',
        'output_table': 'syndata_db.experimental.transactions_a_synth'    
    }
    ],
    'replace_output_tables':TRUE
});
```

执行时间是非线性的，对于完整的数据集，它比数据量所暗示的速度要快得多。  

现在我们导出文件。  

一些准备工作：  

```py
-- export files to unload stage
CREATE STAGE syn_unload 
 DIRECTORY = ( ENABLE = true ) 
 COMMENT = 'export files';

CREATE OR REPLACE FILE FORMAT my_csv_unload_format
  TYPE = 'CSV'
  FIELD_DELIMITER = ','
  FIELD_OPTIONALLY_ENCLOSED_BY = '"';
```

并且导出（小数据集和完整数据集）：  

```py
COPY INTO @syn_unload/transactions_a_5pct_synth 
FROM syndata_db.experimental.transactions_a_5pct_synth
FILE_FORMAT = my_csv_unload_format
HEADER = TRUE;

COPY INTO @syn_unload/transactions_a_synth 
FROM syndata_db.experimental.transactions_a_synth
FILE_FORMAT = my_csv_unload_format
HEADER = TRUE;
```

所以现在我们有了原始的Shopify数据集和合成数据集。是时候进行分析、比较并绘制一些图表了。

# 附录：数据比较与图表  

对于这项分析，我使用了R进行数据处理和可视化。然而，工具的选择并不是最重要的——关键是有一个系统化的数据准备和验证方法。无论你使用R、Python还是其他工具，重要的步骤始终是相同的：  

+   清理并标准化输入数据  

+   验证转化结果  

+   创建可重复的分析  

+   记录关键决策  

详细的代码和可视化技巧实际上可以作为另一篇文章的主题。  

如果你对实现的特定方面感兴趣，随时可以联系我。  
