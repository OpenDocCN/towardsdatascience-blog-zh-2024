# 轻松的数据处理：使用R在多个数据文件中查找变量

> 原文：[https://towardsdatascience.com/effortless-data-handling-find-variables-across-multiple-data-files-with-r-e01c9715b4c7?source=collection_archive---------12-----------------------#2024-11-27](https://towardsdatascience.com/effortless-data-handling-find-variables-across-multiple-data-files-with-r-e01c9715b4c7?source=collection_archive---------12-----------------------#2024-11-27)

## 一种带代码和工作流程的实用解决方案

[](https://medium.com/@rcarrillo90?source=post_page---byline--e01c9715b4c7--------------------------------)[![Rodrigo M Carrillo Larco, MD, PhD](../Images/458ed24625ded66ef47c5af718db3659.png)](https://medium.com/@rcarrillo90?source=post_page---byline--e01c9715b4c7--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--e01c9715b4c7--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--e01c9715b4c7--------------------------------) [Rodrigo M Carrillo Larco, MD, PhD](https://medium.com/@rcarrillo90?source=post_page---byline--e01c9715b4c7--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e01c9715b4c7--------------------------------) ·阅读时间7分钟·2024年11月27日

--

在数据集和无尽的数据字典的迷宫中迷失了？告别繁琐的变量查找吧！了解如何通过两个简单的R函数，快速识别并提取你所需要的变量，轻松处理多个SAS文件。简化你的工作流程，节省时间，让数据准备变得轻松愉快！

![](../Images/dcdeb2464302a41b9c62b63c27bce77f.png)

图片来自[imgix](https://unsplash.com/@imgix?utm_source=medium&utm_medium=referral)在[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

作为一名拥有超过七年健康数据处理经验的研究员，我经常收到包含大量数据集的文件夹。例如，想象一下打开一个包含56个SAS文件的文件夹，每个文件都有独特的数据（见下文示例）。如果你曾经遇到过这种情况，你一定知道那种沮丧的感觉：在成堆的文件中找到特定的变量，感觉就像是在大海捞针。

![](../Images/0692103e729e960899984aad98fa2ab6.png)

这张截图由作者拍摄，显示了本地文件夹。文件名已被模糊处理，以保护数据集的机密性。

初看起来，如果你已经知道你的感兴趣变量在哪里，这似乎不是个问题。但通常，你并不知道。虽然通常会提供数据字典，但它通常是一个PDF文档，列出了跨多页的变量。找到你需要的内容可能需要搜索（Ctrl+F）第100页上的某个变量，结果却发现数据集的名称出现在第10页。来回滚动浪费了很多时间。

![](../Images/45d4e29bf9abe6d1dcc69d3b93f706bd.png)

作者拍摄的数据字典截图。变量名称和标签已被模糊处理，以保持数据集的机密性。

为了避免繁琐的过程，我创建了一个可重复的R工作流，可以读取文件夹中的所有数据集，生成一个包含变量名称及其标签的合并数据框（见下方示例），并识别每个变量的位置。这种方法使我的工作更快、更高效。以下是逐步操作的方法。

![](../Images/4a5d8deb1d639e8e14b1b4f8b920bb56.png)

作者拍摄的`names_labels`数据集的截图（参见步骤2）。变量名称和标签已被模糊处理，以保持数据集的机密性。

**逐步指南**

**步骤 1：使用get_names_labels函数**

首先，使用自定义函数`get_names_labels`（代码见本文末尾）。该函数需要您存储所有数据集的文件夹路径。

```py
path_file <- "D:/folder1/folder2/folder3/folder_with_datasets/"
get_names_labels(path_file)
```

**步骤 2：生成变量字典**

`get_names_labels`函数将创建一个名为`names_labels`的数据框（如上例所示），其中包括：

· 变量名称（variable_name）

· 变量标签（variable_label）

· 变量所在的数据集名称（file_name）

根据数据集的数量和大小，这个过程可能需要一两分钟。

**步骤 3：搜索变量**

一旦生成了`names_labels`数据框，您就可以搜索所需的变量。筛选`variable_name`或`variable_label`列以定位相关术语。例如，如果您在寻找与性别相关的变量，它们可能会被标记为sex、gender、is_male或is_female。

请注意，类似的变量可能出现在多个数据集中。例如，年龄可能出现在主问卷、临床数据集和实验室数据集中。这些变量看起来可能相同，但根据数据的收集方式和地点不同，可能会有所不同。例如：

· 主问卷中的年龄：从所有调查参与者收集。

· 临床/实验室数据集中的年龄：仅限于受邀进一步评估的子集或同意参与的人员。

在这种情况下，主问卷中的变量可能更能代表整个群体。

**步骤 4：识别相关数据集**

确定所需的变量后，筛选`names_labels`数据框以识别包含这些变量的原始数据集（`file_name`）。如果某个变量出现在多个数据集中（例如，`ID`），您需要确定哪个数据集包含您感兴趣的所有变量。

```py
# Say you want these two variables
variables_needed <- c('ID', 'VAR1_A')
names_labels <- names_labels[which(names_labels$variable_name %in% variables_needed), ]
```

如果某个变量在多个原始数据集中都能找到（例如，`ID`），您需要筛选`names_labels`，只保留包含两个变量的原始数据集（例如，`ID`和`VAR1_A`）。在我们的例子中，`names_labels`数据框将只剩下两行，每行对应我们要查找的两个变量，它们都将在同一个原始数据集中找到。

```py
names_labels <- names_labels %>%
  group_by(file_name) %>%
  mutate(count = n()) %>%
  filter(count >= 2)
```

**步骤 5：提取数据**

现在，使用`read_and_select`函数（位于文末）。传入包含相关变量的原始数据集的名称。这个函数会在你的R环境中创建一个新的数据框，仅包含所选择的变量。例如，如果你的变量位于`ABC.sas7bdat`中，函数将创建一个名为`ABC`的新数据框，里面只包含那些变量。

```py
unique(names_labels$file_name) # Sanity check, that there is only one dataframe
read_and_select(unique(names_labels$file_name)[1])
```

**步骤6：清理你的环境**

为了保持工作空间整洁，删除不必要的元素，仅保留你需要的新数据框。例如，如果你的相关变量来自`ABC.sas7bdat`，你将保留过滤后的数据框`ABC`，它是`read_and_select`函数的输出结果。

```py
length(unique(names_labels$file_name))
names_labels$file_name <- str_extract(names_labels$file_name, "[^.]+")
rm(list = setdiff(ls(), c(unique(names_labels$file_name))))
```

**步骤7：合并多个数据集（可选）**

如果你的相关变量分布在多个数据集中（例如`ABC`和`DEF`），你可以将它们合并。使用唯一标识符，如`ID`，将数据集合并成一个单一的数据框。最终结果将是一个统一的数据框，包含你所需的所有变量。你将获得一个`df`数据框，里面包含所有观察值，但只有你需要的变量。

```py
# Get a list with the names of the dataframes in the environment (“ABC” and “DEF”)
object_names <- ls() 

# Get a list with the actual dataframe
object_list <- mget(object_names)

# Reduce the dataframes in the list (“ABC” and “DEF”) by merging conditional on the unique identifier (“ID”)
df <- Reduce(function(x, y) merge(x, y, by = "ID", all = TRUE), object_list)

# Clean your environment to keep only the dataframes (“ABC” and “DEF”) and a new dataframe “df” which will contain all the variables you needed.
rm(object_list, object_names)
```

**为什么这个工作流程有效？**

这种方法节省了时间，并将你的工作组织成一个单一、可重复的脚本。如果你后来决定添加更多变量，只需重新访问`步骤2和3`，更新你的列表，并重新运行脚本。这种灵活性在处理大数据集时非常宝贵。虽然你仍然需要查阅文档以理解变量定义和数据收集方法，但这种工作流程减少了定位和准备数据的工作量。处理多个数据集不必让人感到不知所措。通过使用像`get_names_labels`和`read_and_select`这样的自定义函数，你可以简化数据准备的工作流程。

***你是否在处理多个数据集时遇到过类似的挑战？如果有，欢迎在评论中分享你的想法或小贴士，或者如果你觉得这篇文章有帮助，给它点赞。让我们继续交流，互相学习！***

以下是两个自定义函数。将它们保存在一个`R`脚本文件中，并在需要时将脚本加载到你的工作环境中。例如，你可以将文件保存为`_Functions.R`，以便随时访问。

```py
# You can load the functions as
source('D:/Folder1/Folder2/Folder3/_Functions.R')
```

```py
library(haven)
library(tidyverse)
library(stringr)

## STEPS TO USE THESE FUNCTIONS:
## 1\. DEFINE THE OBJECT 'PATH_FILE', WHICH IS A PATH TO THE DIRECTORY WHERE
##    ALL THE DATASETS ARE STORED.
## 2\. APPLY THE FUNCTION 'get_names_labels' WITH THE PATH. THE FUNCTION WILL 
##    RETURN A DATAFRAME NAMES 'names_labels'. 
## 3\. THE FUNCTION WILL RETURN A DATASET ('names_labels) SHOWING THE NAMES OF 
##    THE VARIABLES, THE LABELS, AND THE DATASET. VISUALLY/MANUALLY EXPLORE THE 
##    DATASET TO SELECT THE VARIABLES WE NEED. CREATE A VECTOR WITH THE NAMES 
##    OF THE VARIABLES WE NEED, AND NAME THIS VECTOR 'variables_needed'.
## 4\. FROM THE DATASET 'names_labels', KEEP ONLY THE ROWS WITH THE VARIABLES WE
##    WILL USE (STORED IN THE VECTOR 'variables_needed').
## 5\. APPLY THE FUNCTION 'read_and_select' TO EACH OF THE DATASETS WITH RELEVANT 
##    VARIABLES. THIS FUNCTION WILL ONLY NEED THE NAME OF THE DATASET, WHICH IS
##    STORED IN THE LAST COLUMN OF DATASET 'names_labels'.

### FUNCTION TO 1) READ ALL DATASETS IN A FOLDER; 2) EXTRACT NAMES AND LABELS;
### 3) PUT NAMES AND LABELS IN A DATASET; AND 4) RETURN THE DATASET. THE ONLY
### INPUT NEEDED IS A PATH TO A DIRECTORY WHERE ALL THE DATASETS ARE STORED.

get_names_labels <- function(path_file){
  results_df <- list()

  sas_files <- c(
    list.files(path = path_file, pattern = "\\.sas7bdat$")
  )

  for (i in 1:length(sas_files)) {
    print(sas_files[i])

    # Read the SAS file
    sas_data <- read_sas(paste0(path_file, sas_files[i]))
    sas_data <- as.data.frame(sas_data)

    # Get the variable names and labels
    var_names <- names(sas_data)
    labels <- sas_data %>% 
      map(~attributes(.)$label) %>%
      map_chr(~ifelse(is.null(.), NA, .))

    # Combine the variable names and labels into a data frame
    var_df <- data.frame(
      variable_name = var_names,
      variable_label = labels,
      file_name = sas_files[i],
      stringsAsFactors = FALSE
    )

    # Append the results to the overall data frame
    results_df[[i]] <- var_df
  }

  results_df <- do.call(rbind, results_df)

  #return(results_df)
  assign('names_labels', results_df, envir = .GlobalEnv)

}

################################################################################

### FUNCTION TO READ EACH DATASET AND KEEP ONLY THE VARIABLES WE SELECTED; THE
### FUNCTION WILL SAVE EACH DATASET IN THE ENVIRONMENT. THE ONLY INPUNT IS THE
### NAME OF THE DATASET.

read_and_select <- function(df_file){

  df_tmp <- read_sas(paste0(path_file, df_file))

  df_tmp <- df_tmp %>%
    select(unique(names_labels[which(names_labels$file_name == df_file), ]$variable_name)) %>%
    as.data.frame()

  assign(str_extract(df_file, "[^.]+"), df_tmp,envir = .GlobalEnv)

}

################################################################################
```

*你可以在* [*LinkedIn*](https://www.linkedin.com/in/rodrigo-m-carrillo-larco-md-phd-39016a27/) *上找到我，期待与你联系并讨论。*
