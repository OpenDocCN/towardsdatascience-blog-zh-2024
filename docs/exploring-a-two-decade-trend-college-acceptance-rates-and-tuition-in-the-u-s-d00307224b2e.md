# 探索二十年的趋势：美国大学录取率与学费

> 原文：[https://towardsdatascience.com/exploring-a-two-decade-trend-college-acceptance-rates-and-tuition-in-the-u-s-d00307224b2e?source=collection_archive---------2-----------------------#2024-01-26](https://towardsdatascience.com/exploring-a-two-decade-trend-college-acceptance-rates-and-tuition-in-the-u-s-d00307224b2e?source=collection_archive---------2-----------------------#2024-01-26)

## 如今，进入大学是否变得更加困难？

[](https://medium.com/@ryuryu09030903?source=post_page---byline--d00307224b2e--------------------------------)[![Ryu Sonoda](../Images/52445252872ed381dd86d3ada5665e1b.png)](https://medium.com/@ryuryu09030903?source=post_page---byline--d00307224b2e--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--d00307224b2e--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--d00307224b2e--------------------------------) [Ryu Sonoda](https://medium.com/@ryuryu09030903?source=post_page---byline--d00307224b2e--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d00307224b2e--------------------------------) ·10分钟阅读·2024年1月26日

--

**背景** 作为一名刚刚毕业的格林内尔学院校友，我密切关注并深受学术领域重大变化的影响。当我毕业时，格林内尔的录取率从我入学时的水平下降了15%，与此同时学费也急剧上升。这一趋势并非仅限于我的母校；来自不同大学的朋友们也分享了类似的经历。

这让我开始思考：这是美国各大学普遍的趋势吗？我的理论有两个方面：首先，在线申请的兴起可能简化了向多所大学申请的过程，从而增加了申请人数并降低了录取率。其次，[移民政策研究所](https://www.migrationpolicy.org/article/international-students-united-states)的一篇文章提到，从2000年到2020年，美国国际学生人数翻了一番（从50万增加到100万），这可能加剧了竞争。同时，我对2001年到2022年的学费趋势也很感兴趣。我的目标是通过数据可视化揭示这些模式。以下分析中的所有图片，除非另有说明，均为作者提供！

**数据集** 我使用的数据集包含了关于美国大学从 2001 年到 2022 年的一系列数据，涵盖了学校类型、年度录取率、州所在位置和学费等方面。数据来源于[大学评分卡](https://collegescorecard.ed.gov/data/)，原始数据集庞大，包含了超过 3,000 列和 10,000 行。我精心挑选了相关列进行聚焦分析，最终得到了在[Kaggle](https://www.kaggle.com/datasets/ryusonoda/u-s-university-dataset-from-2001-to-2022/data)上提供的精炼数据集。为了确保数据的相关性和完整性，我专注于美国新闻大学排名中的四年制大学，并从[这里](https://andyreiter.com/datasets/)获取了该列表。

**录取率变化趋势**

让我们深入探讨过去二十年间大学录取率的变化。起初，我怀疑我会观察到一个稳定的下降趋势。图 1 展示了从 2001 年到 2022 年的这一变化轨迹。可以明显看到，直到 2008 年，录取率持续下降，之后出现波动，直到 2020-2021 年左右出现显著增长，这可能是由于 COVID-19 大流行影响了间隔年决定和入学策略。

```py
avg_acp_ranked = df_ranked.groupby("year")["ADM_RATE_ALL"].mean().reset_index()

plt.figure(figsize=(10, 6))  # Set the figure size 
plt.plot(avg_acp_ranked['year'], avg_acp_ranked['ADM_RATE_ALL'], marker='o', linestyle='-', color='b', label='Acceptance Rate')
plt.title('Average Acceptance Rate Over the Years')  # Set the title
plt.xlabel('Year')  # Label for the x-axis
plt.ylabel('Average Acceptance Rate')  # Label for the y-axis
plt.grid(True)  # Show grid 

# Show a legend 
plt.legend()
# Display the plot
plt.show()
```

![](../Images/edcf234932cd9def99ac1caf7efcd03f.png)

*图 1*

然而，总体下降的幅度并不像我在格林内尔大学的经历那样陡峭。相比之下，当我们放大查看更具声望的大学的录取率（图 2），我们可以清晰地看到其逐步下降的趋势。这使我将大学按其 2022 年的录取率分为三类（前 10% 的竞争性大学、前 50% 的大学和其他大学），并分析这些细分领域的趋势。

```py
pres_colleges = ["Princeton University", "Massachusetts Institute of Technology", "Yale University", "Harvard University", "Stanford University"]
pres_df = df[df['INSTNM'].isin(pres_colleges)]
pivot_pres = pres_df.pivot_table(index="INSTNM", columns="year", values="ADM_RATE_ALL")
pivot_pres.T.plot(linestyle='-')
plt.title('Change in Acceptance Rate Over the Years')
plt.xlabel('Year')
plt.ylabel('Acceptance Rate')
plt.legend(title='Colleges')
plt.show()
```

![](../Images/2f3484d9f108c63d52a5a84e998ecd39.png)

*图 2*

图 3 展示了一些令人惊讶的洞察。除了竞争最弱的 50% 外，大学的录取率自 2001 年以来普遍有所增加。2008 年后，除前 10% 的大学外，其他大学的波动可以归因于经济因素，如衰退。值得注意的是，竞争激烈的大学并没有像其他地方那样经历因疫情引发的录取率激增。

```py
top_10_threshold_ranked = df_ranked[df_ranked["year"] == 2001]["ADM_RATE_ALL"].quantile(0.1)
top_50_threshold_ranked = df_ranked[df_ranked["year"] == 2001]["ADM_RATE_ALL"].quantile(0.5)

top_10 = df_ranked[(df_ranked["year"]==2001) & (df_ranked["ADM_RATE_ALL"] <= top_10_threshold_ranked)]["UNITID"]
top_50 = df_ranked[(df_ranked["year"]==2001) & (df_ranked["ADM_RATE_ALL"] > top_10_threshold_ranked) & (df_ranked["ADM_RATE_ALL"] <= top_50_threshold_ranked)]["UNITID"]
others = df_ranked[(df_ranked["year"]==2001) & (df_ranked["ADM_RATE_ALL"] > top_50_threshold_ranked)]["UNITID"]

top_10_df = df_ranked[df_ranked["UNITID"].isin(top_10)]
top50_df = df_ranked[df_ranked["UNITID"].isin(top_50)]
others_df = df_ranked[df_ranked["UNITID"].isin(others)]

avg_acp_top10 = top_10_df.groupby("year")["ADM_RATE_ALL"].mean().reset_index()
avg_acp_others = others_df.groupby("year")["ADM_RATE_ALL"].mean().reset_index()
avg_acp_top50 = top50_df.groupby("year")["ADM_RATE_ALL"].mean().reset_index()

plt.figure(figsize=(10, 6))  # Set the figure size 
plt.plot(avg_acp_top10['year'], avg_acp_top10['ADM_RATE_ALL'], marker='o', linestyle='-', color='g', label='Top 10%')
plt.plot(avg_acp_top50['year'], avg_acp_top50['ADM_RATE_ALL'], marker='o', linestyle='-', color='b', label='Top 50%')
plt.plot(avg_acp_others['year'], avg_acp_others['ADM_RATE_ALL'], marker='o', linestyle='-', color='r', label='Others')
plt.title('Average Acceptance Rate Over the Years')  # Set the title
plt.xlabel('Year')  # Label for the x-axis
plt.ylabel('Average Acceptance Rate')  # Label for the y-axis

# Show a legend 
plt.legend()
# Display the plot
plt.show()
```

![](../Images/f573997329472ca67f904b096a40e744.png)

*图 3*

有一项发现特别让我感兴趣：当考虑前 10% 的大学时，它们的录取率在这些年里并没有显著下降。这让我质疑，竞争的变化是否是广泛的，还是一些大学变得明显更难或更容易入学。名校录取率的稳定下降（见*图 2*）暗示了后者的情况。

为了更清晰地了解情况，我可视化了从 2001 年到 2022 年间大学竞争力的变化。图 4 展示了一个令人惊讶的趋势：大约一半的大学实际上变得不那么具有竞争力，这与我最初的预期相反。

```py
pivot_pres_ranked = df_ranked.pivot_table(index="INSTNM", columns="year", values="ADM_RATE_ALL")
pivot_pres_ranked_down = pivot_pres_ranked[pivot_pres_ranked[2001] >= pivot_pres_ranked[2022]]
len(pivot_pres_ranked_down)

pivot_pres_ranked_up = pivot_pres_ranked[pivot_pres_ranked[2001] < pivot_pres_ranked[2022]]
len(pivot_pres_ranked_up)

categories = ["Up", "Down"]
values = [len(pivot_pres_ranked_up), len(pivot_pres_ranked_down)]

plt.figure(figsize=(8, 6))
plt.bar(categories, values, width=0.4, align='center', color=["blue", "red"])
plt.xlabel('Change in acceptance rate')
plt.ylabel('# of colleges')
plt.title('Change in acceptance rate from 2001 to 2022')

# Show the chart
plt.tight_layout()
plt.show()
```

![](../Images/f234a0b01d9dcad2a26cc8bf87792a59.png)

*图 4*

这促使我探讨可能影响这些变化的因素。我的假设，得到图 2 的支持，是已经具有选择性的大学随着时间的推移变得更加具有选择性。图 5 比较了 2001 年和 2022 年的接受率。

45 度线划分了变得更具竞争力或变得不那么具有竞争力的大学。那些位于线下的大学接受率有所降低。左下象限中一个明显的簇群代表了那些变得愈加排他的选择性大学。这一趋势得到了这样的观察的支持：最初接受率低的大学（图的左侧）往往会跌破这条分割线，而右侧的大学则分布较为均匀。

此外，值得注意的是，自 2001 年以来，最具选择性的大学主要是私立大学。为了测试顶端和底端 50 百分位大学之间接受率变化是否有显著差异，我进行了独立样本 t 检验（原假设：θ_top = θ_bottom）。结果显示，存在统计学上显著的差异。

```py
import seaborn as sns
from matplotlib.patches import Ellipse

pivot_region = pd.merge(pivot_pres_ranked[[2001, 2022]], df_ranked[["REGION","INSTNM", "UNIVERSITY", "CONTROL"]], on="INSTNM", how="right")

plt.figure(figsize=(8, 8))
sns.scatterplot(data=pivot_region, x=2001, y=2022, hue='CONTROL', palette='Set1', legend='full')
plt.xlabel('Acceptance rate for 2001')
plt.ylabel('Acceptance rate for 2022')
plt.title('Change in acceptance rate')

x_line = np.linspace(0, max(pivot_region[2001]), 100)  # X-values for the line
y_line = x_line  # Y-values for the line (slope = 1)

plt.plot(x_line, y_line, label='45-Degree Line', color='black', linestyle='--')
# Define ellipse parameters (center, width, height, angle)
ellipse_center = (0.25, 0.1)  # Center of the ellipse
ellipse_width = 0.4  # Width of the ellipse
ellipse_height = 0.2  # Height of the ellipse
ellipse_angle = 45  # Rotation angle in degrees

# Create an Ellipse patch
ellipse = Ellipse(
    xy=ellipse_center,
    width=ellipse_width,
    height=ellipse_height,
    angle=ellipse_angle,
    edgecolor='b',  # Edge color of the ellipse
    facecolor='none',  # No fill color (transparent)
    linewidth=2  # Line width of the ellipse border
)

plt.gca().add_patch(ellipse)

# Add the ellipse to the current a

plt.legend()
plt.gca().set_aspect('equal')
plt.show()
```

![](../Images/668c84cf5e37509b23685c3a1bd39509.png)

*图 5*

另一个引起我好奇的方面是区域差异。图 6 列出了接受率下降最显著的前五所大学（计算方式为 2022 年接受率除以 2001 年接受率）。

看到芝加哥大学二十年前的接受率是如此之高真令人吃惊——当时一半的申请者都能被录取！

这也帮助我理解了自己最初对接受率普遍下降的偏见；值得注意的是，我的母校格林内尔学院正是这五所大学之一，其接受率大幅下降。

有趣的是，排名前五的大学中有三所位于中西部。我的理论是，随着互联网的兴起，这些历史上并不像东西海岸的大学那样著名的院校，在国内外的知名度有了显著提高。

```py
pivot_pres_ranked["diff"] = pivot_pres_ranked[2001] / pivot_pres_ranked[2022]
tmp = pivot_pres_ranked.reset_index()
tmp = tmp.merge(df_ranked[df_ranked["year"]==2022][["INSTNM", "STABBR", "CITY"]],on="INSTNM")
tmp.sort_values(by="diff",ascending=False)[["INSTNM", "diff", "STABBR", "CITY"]].head(5)
```

![](../Images/c4669731bfb46e44188d261ccabbc839.png)

*图 6*

在接下来的章节中，我们将探讨学费趋势及其与这些接受率变化的相关性，深入了解塑造现代美国高等教育的动态。

**学费变化** 分析过去二十年来的学费趋势揭示了一些令人吃惊的模式。图 7 展示了不同类别的学费平均值：私立、州内公立、州外公立以及整体学费。所有类别的学费都有稳步上升的趋势。

值得注意的是，私立大学的学费涨幅高于公立大学，而州内公立大学的学费涨幅相对较为温和。然而，令人震惊的是，整体学费自 2001 年以来已经翻倍，从 15,000 美元飙升至 35,000 美元。

```py
avg_tuition = df_ranked.groupby('year')["TUITIONFEE_OUT"].mean().reset_index()
avg_tuition_private = df_ranked[df_ranked['CONTROL'] != "Public"].groupby('year')["TUITIONFEE_OUT"].mean().reset_index()
avg_tuition_public_out = df_ranked[df_ranked['CONTROL'] == "Public"].groupby('year')["TUITIONFEE_OUT"].mean().reset_index()
avg_tuition_public_in = df_ranked[df_ranked['CONTROL'] == "Public"].groupby('year')["TUITIONFEE_IN"].mean().reset_index()

plt.figure(figsize=(10, 6))  # Set the figure size (optional)
plt.plot(avg_tuition_public_out['year'], avg_tuition_public_out['TUITIONFEE_OUT'], marker='o', linestyle='-', color='g', label='Out-state Tuition for Public')
plt.plot(avg_tuition_public_in['year'], avg_tuition_public_in['TUITIONFEE_IN'], marker='o', linestyle='-', color='y', label='In-state Tuition for Public')
plt.plot(avg_tuition_private['year'], avg_tuition_private['TUITIONFEE_OUT'], marker='o', linestyle='-', color='r', label='Tuition for Private')
plt.plot(avg_tuition['year'], avg_tuition['TUITIONFEE_OUT'], marker='o', linestyle='-', color='b', label='Tuition for All')

plt.title('Average Tuition Over the Years')  # Set the title
plt.xlabel('Year')  # Label for the x-axis
plt.ylabel('Average Tuition')  # Label for the y-axis

# Show a legend 
plt.legend()
# Display the plot
plt.show()
```

![](../Images/a7a93476f8a82f1af32fc2724a738ad1.png)

*图 7*

有人可能会认为，这一增长与一般的经济通货膨胀一致，但与通货膨胀率的对比呈现出不同的图景（图 8）。除了过去两年因疫情导致通货膨胀急剧上升外，学费上涨一直超过了通货膨胀率。

尽管学费上涨的模式与通货膨胀的模式相似，但需要注意的是，与在 2009 年出现负增长的通货膨胀不同，学费上涨从未低于零。尽管增长速度有所放缓，但人们希望它最终能稳定下来，停止学费成本的上升趋势。

```py
avg_tuition['Inflation tuition'] = avg_tuition['TUITIONFEE_OUT'].pct_change() * 100
avg_tuition.iloc[0,2] = 1
avg_tuition

plt.figure(figsize=(10, 6))  # Set the figure size 
plt.plot(df_inflation['year'], df_inflation['Inflation rate'], marker='o', linestyle='-', color='r', label='Inflation')
plt.plot(avg_tuition['year'],avg_tuition['Inflation tuition'], marker='o', linestyle='-', color='b', label='Tuition')
plt.title('Increase in Tuition and Inflation Over the Years')  # Set the title
plt.xlabel('Year')  # Label for the x-axis
plt.ylabel('Rate')  # Label for the y-axis

# Show a legend 
plt.legend()
# Display the plot
plt.show()
```

![](../Images/d19ecf56543a2c8c01c33c88a051378f.png)

*图 8*

在探索那些学费上涨较为显著的大学特点时，我假设更具选择性的大学由于需求较高，可能会出现较大的学费上涨。图 9 考察了这一理论。与预期相反，数据并没有显示选择性与学费上涨之间有明显的相关趋势。学费的变化似乎在各个录取率之间徘徊在 2.2 倍的平均值附近。然而，值得注意的是，几乎所有具有较高选择性的大学的学费都翻了一番以上，而其他大学的分布则更加多样化。这表明，相较于选择性较低的大学，选择性较高的大学在学费变化上的标准差较低。

```py
tuition_pivot = df_ranked.pivot_table(index="INSTNM", columns="year", values="TUITIONFEE_OUT")
tuition_pivot["TUI_CHANGE"] = tuition_pivot[2022]/tuition_pivot[2001]
tuition_pivot = tuition_pivot[tuition_pivot["TUI_CHANGE"] < 200]
print(tuition_pivot["TUI_CHANGE"].isnull().sum())
tmp = pd.merge(tuition_pivot["TUI_CHANGE"], df_ranked[df_ranked["year"]==2022][["ADM_RATE_ALL", "INSTNM", "REGION", "STABBR", "CONTROL"]], on="INSTNM", how="right")
plt.figure(figsize=(8, 8))
sns.scatterplot(data=tmp, x="ADM_RATE_ALL", y="TUI_CHANGE", palette='Set2', legend='full')
plt.xlabel('Acceptance rate in 2022')
plt.ylabel('Change in Tuition')
plt.title('Acceptance rate vs Change in Tuition')

plt.legend()
plt.show()
```

![](../Images/51f7b987a8b446ee5e6eb1bf17c7c079.png)

*图 9*

在检查了录取率与学费上涨之间的关系后，我将注意力转向了地区因素。我假设受科技公司经济激增影响的西海岸学校可能经历了显著的学费上涨。为验证这一假设，我在图 10 中展示了各州的学费增长情况。

与我的预期相反，西海岸并不是学费上涨最多的地区。相反，像俄克拉荷马州和犹他州这样的州经历了显著的学费上涨，而南达科他州和新墨西哥州的涨幅最小。尽管有一些例外，整体趋势表明，西部各州的学费上涨普遍超过了东部各州。

```py
import geopandas as gpd

sta_tui = tmp.groupby("STABBR")["TUI_CHANGE"].mean()
sta_tui = sta_tui.reset_index()

shapefile_path = "path_to_shape_file"  
gdf = gpd.read_file(shapefile_path)

sta_tui["STUSPS"] = sta_tui["STABBR"]
merged_data = gdf.merge(sta_tui, on="STUSPS", how="left")
final = merged_data.drop([42, 44, 45, 38, 13])

# Plot the choropleth map
fig, ax = plt.subplots(1, 1, figsize=(16, 20))
final.plot(column='TUI_CHANGE', cmap="Reds", ax=ax, linewidth=0.3, edgecolor='0.8', legend=True)
ax.set_title('Average Change in Tuition over across the U.S.')
plt.axis('off')  # Turn off axis
plt.legend(fontsize=6)
plt.show()
```

![](../Images/27ab9b992a6fdf4ee1392242be9185a7.png)

*图 10*

**未来方向与局限性**

尽管这一分析提供了基于单年度录取率变化与学费变化的见解，但通过 5 年平均值的比较，可以获得更全面的视角。在我使用这种方法进行的初步分析中，结论与之前类似。

使用的数据集还包含了许多其他属性，如种族比例、平均SAT成绩和家庭收入中位数。然而，由于较旧数据中的缺失值，我没有使用这些因素。通过聚焦于近年来的数据，这些额外的因素可能提供更深入的见解。对于那些有兴趣进一步探索的人，数据集可以在[Kaggle](https://www.kaggle.com/datasets/ryusonoda/u-s-university-dataset-from-2001-to-2022/data)上获取。

需要注意的是，这一分析基于美国新闻排名的大学，可能引入了一定的偏差。观察到的趋势可能与美国大学的整体状况有所不同。

对于数据爱好者，我的[代码](https://www.kaggle.com/code/ryusonoda/eda-university)和方法论可以进一步探索。我邀请你深入研究，也许能够发现新的视角或验证这些发现。感谢你和我一同踏上这段数据驱动的美国高等教育变革之旅！

# 来源

[1] Emma Israel 和 Jeanne Batalova. “美国的国际学生”（2021年1月14日）。[https://www.migrationpolicy.org/article/international-students-united-states](https://www.migrationpolicy.org/article/international-students-united-states)

[2] 美国教育部大学排名（最后更新于2023年10月10日）。公共领域，[https://will-stanton.com/creating-a-great-data-science-resume/](https://collegescorecard.ed.gov/data/)

[3] Andrew G. Reiter, “美国新闻与世界报道历史性文理学院及大学排名” [http://andyreiter.com/datasets/](http://andyreiter.com/datasets/)
