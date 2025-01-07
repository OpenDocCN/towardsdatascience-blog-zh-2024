# 可解释的通用机器学习管道与MLflow

> 原文：[https://towardsdatascience.com/explainable-generic-ml-pipeline-with-mlflow-2494ca1b3f96?source=collection_archive---------5-----------------------#2024-11-26](https://towardsdatascience.com/explainable-generic-ml-pipeline-with-mlflow-2494ca1b3f96?source=collection_archive---------5-----------------------#2024-11-26)

## 一个端到端的示范，将预处理器和解释器包装成一个算法无关的机器学习管道，使用`mlflow.pyfunc`

[](https://menawang.medium.com/?source=post_page---byline--2494ca1b3f96--------------------------------)[![Mena Wang, PhD](../Images/eac9fa55026f9fc119bc868439ff311b.png)](https://menawang.medium.com/?source=post_page---byline--2494ca1b3f96--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--2494ca1b3f96--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--2494ca1b3f96--------------------------------) [Mena Wang, PhD](https://menawang.medium.com/?source=post_page---byline--2494ca1b3f96--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--2494ca1b3f96--------------------------------) ·13分钟阅读·2024年11月26日

--

![](../Images/718ec8036048d1449b127442c59434ab.png)

图片由 [Hannah Murrell](https://unsplash.com/@hannahj236?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) 提供，来源 [Unsplash](https://unsplash.com/photos/person-holding-ball-focus-on-tree-pTfdcT0hxGc?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

# **介绍**

MLOps中的一个常见挑战是迁移不同算法或框架时的麻烦。为了解决这个问题，这是我关于使用`mlflow.pyfunc`进行通用模型构建的第二篇文章。

在我之前的文章中，我提供了一个适合初学者的逐步示范，展示如何创建一个极简的算法无关模型包装器。

[](/algorithm-agnostic-model-building-with-mlflow-b106a5a29535?source=post_page-----2494ca1b3f96--------------------------------) [## 使用MLflow进行算法无关模型构建

### 一个适合初学者的逐步指南，展示如何使用mlflow.pyfunc创建通用机器学习管道

towardsdatascience.com](/algorithm-agnostic-model-building-with-mlflow-b106a5a29535?source=post_page-----2494ca1b3f96--------------------------------)

为了推进我们的旅程，在本文结束时，我们将构建一个更为复杂的机器学习管道，具备以下功能：

1.  该管道支持分类（二分类）和回归任务。它适用于scikit-learn模型以及其他遵循scikit-learn接口的算法（即，fit、predict/predict_proba）。

1.  引入一个功能完备的`预处理器`，它可以在训练数据上拟合，然后用于转换新数据，以供模型使用。这个预处理器可以处理数值型和类别型特征，并能通过各种插补策略处理缺失值。

1.  添加一个`explainer`来阐明模型的推理过程，这对于模型选择、监控和实现至关重要。由于不同机器学习算法对SHAP值的实现各异，这项任务可能会很棘手。但没问题，我们将在本文中解决这个挑战。😎

与前一篇文章一致，

1.  你将看到切换不同自定义预处理器是多么简单，类似于切换不同的机器学习算法。

1.  这个机器学习管道将所有自定义的管道元素封装在背后，同时仍然提供统一的`pyfunc`模型表示，以简化模型的部署、重新部署和下游评分。

🔗 所有代码和配置可以在[GitHub](https://github.com/MenaWANG/mlflow-demo/blob/main/pyfunc_pipeline.ipynb)上找到。🧰

# **预处理器（V1）**

许多机器学习算法——例如线性模型（如线性回归、支持向量机）、基于距离的模型（如KNN、PCA）以及基于梯度的模型（如梯度提升方法或梯度下降优化）——通常在对输入特征进行缩放后表现更好，因为缩放可以防止具有较大范围的特征主导学习过程。此外，现实世界中的数据通常包含缺失值。因此，在这个第一版中，我们将构建一个预处理器，它可以训练来缩放新数据并填充缺失值，为模型的使用做准备。

一旦这个预处理器构建完成，我将演示如何轻松地将它集成到`pyfunc`机器学习管道中。听起来不错吧？我们开始吧。🤠

```py
class PreProcessor(BaseEstimator, TransformerMixin):
    """
    Custom preprocessor for numeric features.

    - Handles scaling of numeric data
    - Performs imputation of missing values

    Attributes:
        transformer (Pipeline): Pipeline for numeric preprocessing
        features (List[str]): Names of input features
    """

    def __init__(self):
        """
        Initialize preprocessor.

        - Creates placeholder for transformer pipeline
        """
        self.transformer = None

    def fit(self, X, y=None):
        """
        Fits the transformer on the provided dataset.

        - Configures scaling for numeric features
        - Sets up imputation for missing values
        - Stores feature names for later use

        Parameters:
            X (pd.DataFrame): The input features to fit the transformer.
            y (pd.Series, optional): Target variable, not used in this method.

        Returns:
            PreProcessor: The fitted transformer instance.
        """
        self.features = X.columns.tolist()

        if self.features:
            self.transformer = Pipeline(steps=[
                ('imputer', SimpleImputer(strategy='median')),
                ('scaler', StandardScaler())
            ])
            self.transformer.fit(X[self.features])

        return self

    def transform(self, X):
        """
        Transform input data using fitted pipeline.

        - Applies scaling to numeric features
        - Handles missing values through imputation

        Parameters:
            X (pd.DataFrame): Input features to transform

        Returns:
            pd.DataFrame: Transformed data with scaled and imputed features
        """
        X_transformed = pd.DataFrame()

        if self.features:
            transformed_data = self.transformer.transform(X[self.features])
            X_transformed[self.features] = transformed_data

        X_transformed.index = X.index

        return X_transformed

    def fit_transform(self, X, y=None):
        """
        Fits the transformer on the input data and then transforms it.

        Parameters:
            X (pd.DataFrame): The input features to fit and transform.
            y (pd.Series, optional): Target variable, not used in this method.

        Returns:
            pd.DataFrame: The transformed data.
        """
        self.fit(X, y)
        return self.transform(X)
```

这个预处理器可以在训练数据上进行拟合，然后用于处理任何新的数据。它将成为下面机器学习管道中的一个元素，但当然，我们也可以独立使用或测试它。让我们创建一个合成数据集，并使用预处理器来转换它。

```py
# Set parameters for synthetic data
n_feature = 10
n_inform = 4
n_redundant = 0
n_samples = 1000

# Generate synthetic classification data
X, y = make_classification(
    n_samples=n_samples,
    n_features=n_feature,
    n_informative=n_inform,
    n_redundant=n_redundant,
    shuffle=False,
    random_state=12
)

# Create feature names
feat_names = [f'inf_{i+1}' for i in range(n_inform)] + \
            [f'rand_{i+1}' for i in range(n_feature - n_inform)]

# Convert to DataFrame with named features
X = pd.DataFrame(X, columns=feat_names)

# Split data into train and test sets
X_train, X_test, y_train, y_test = train_test_split(
    X, y,
    test_size=0.2,
    random_state=22
)
```

以下是{sweetViz}报告在缩放前后的截图；你可以看到，缩放没有改变每个特征分布的基本形状，只是重新缩放并移动了它。顺便说一下，只需要两行代码就能生成一份非常全面的EDA报告，{sweetViz}的代码可以在上面链接的GitHub仓库中找到。🥂

![](../Images/7a1b3029a4739afeff34a9d50170c3d3.png)

预处理前后SweetViz报告的截图

# **带预处理器的机器学习管道**

现在，让我们创建一个`mlflow.pyfunc`风格的机器学习管道，它可以封装这个预处理器。

```py
class ML_PIPELINE(mlflow.pyfunc.PythonModel):
    """
    Custom ML pipeline for classification and regression.

    - work with any scikit-learn compatible model
    - Combines preprocessing and model training
    - Handles model predictions
    - Compatible with MLflow tracking
    - Supports MLflow deployment

    Attributes:
        model (BaseEstimator or None): A scikit-learn compatible model instance
        preprocessor (Any or None): Data preprocessing pipeline
        config (Any or None): Optional config for model settings 
        task(str): Type of ML task ('classification' or 'regression')
    """

    def __init__(self, model=None, preprocessor=None, config=None):
        """
        Initialize the ML_PIPELINE.

        Parameters:
            model (BaseEstimator, optional): 
                - Scikit-learn compatible model
                - Defaults to None

            preprocessor (Any, optional):
                - Transformer or pipeline for data preprocessing
                - Defaults to None

            config (Any, optional):
                - Additional model settings
                - Defaults to None
        """
        self.model = model
        self.preprocessor = preprocessor
        self.config = config
        self.task = "classification" if hasattr(self.model, "predict_proba") else "regression"

    def fit(self, X_train: pd.DataFrame, y_train: pd.Series):
        """
        Train the model on provided data.

        - Applies preprocessing to features
        - Fits model on transformed data

        Parameters:
            X_train (pd.DataFrame): Training features
            y_train (pd.Series): Target values
        """
        X_train_preprocessed = self.preprocessor.fit_transform(X_train.copy())
        self.model.fit(X_train_preprocessed, y_train)

    def predict(
            self, context: Any, model_input: pd.DataFrame
            ) -> np.ndarray:
        """
        Generate predictions using trained model.

        - Applies preprocessing to new data
        - Uses model to make predictions

        Parameters:
            context (Any): Optional context information provided 
                by MLflow during the prediction phase
            model_input (pd.DataFrame): Input features

        Returns:
            Any: Model predictions or probabilities
        """
        processed_model_input = self.preprocessor.transform(model_input.copy())
        if self.task == "classification":
            prediction = self.model.predict_proba(processed_model_input)[:,1]
        elif self.task == "regression":
            prediction = self.model.predict(processed_model_input)
        return prediction
```

上面定义的机器学习管道将预处理器和机器学习算法作为参数。以下是使用示例

```py
# define the ML pipeline instance with lightGBM classifier
ml_pipeline = ML_PIPELINE(model = lgb.LGBMClassifier(),
                          preprocessor = PreProcessor())
```

就是这么简单！🎉 如果你想尝试其他算法，只需像下面一样交换即可。作为包装器，它可以封装回归和分类算法。对于后者，将返回预测的概率，如上例所示。

```py
# define the ML pipeline instance with random forest regressor
ml_pipeline = ML_PIPELINE(model = RandomForestRegressor(),
                          preprocessor = PreProcessor())
```

如下方代码片段所示，向算法传递超参数非常简单，这使得该ML管道成为超参数调优的完美工具。我将在后续的文章中详细讲解这个话题。

```py
params = {
    'n_estimators': 100,
    'max_depth': 6,
    'learning_rate': 0.1 
}
model = xgb.XGBClassifier(**params)
ml_pipeline = ML_PIPELINE(model = model,
                          preprocessor = PreProcessor())
```

因为这个ML管道是基于`mlflow.pyfunc`版本构建的。我们可以使用`mlflow`自动保存的丰富元数据进行日志记录，供下游使用。部署后，我们可以将元数据作为`context`传递给模型，在`predict`函数中使用，如下所示。更多信息和演示可以在我之前的文章中找到，链接已在文中给出。

```py
# train the ML pipeline
ml_pipeline.fit(X_train, y_train)

# use the trained pipeline for prediction
y_prob = ml_pipeline.predict(
    context=None, # provide metadata for model in production
    model_input=X_test
)
auc = roc_auc_score(y_test, y_prob)
print(f"auc: {auc:.3f}")
```

# 预处理器（V2）

上面的预处理器到目前为止表现良好，但我们将通过下面的两种方式进行改进，然后展示如何轻松切换预处理器。

1.  允许用户自定义预处理过程。例如，指定填充策略。

1.  扩展预处理器的能力，以处理类别特征。

```py
 class PreProcessor_v2(BaseEstimator, TransformerMixin):
    """
    Custom transformer for data preprocessing.

    - Scales numeric features
    - Encodes categorical features
    - Handles missing values via imputation
    - Compatible with scikit-learn pipeline

    Attributes:
        num_impute_strategy (str): Numeric imputation strategy
        cat_impute_strategy (str): Categorical imputation strategy
        num_transformer (Pipeline): Numeric preprocessing pipeline
        cat_transformer (Pipeline): Categorical preprocessing pipeline
        transformed_cat_cols (List[str]): One-hot encoded column names
        num_features (List[str]): Numeric feature names
        cat_features (List[str]): Categorical feature names
    """

    def __init__(self, num_impute_strategy='median', 
                 cat_impute_strategy='most_frequent'):
        """
        Initialize the transformer.

        - Sets up numeric data transformer
        - Sets up categorical data transformer
        - Configures imputation strategies

        Parameters:
            num_impute_strategy (str): Strategy for numeric missing values
            cat_impute_strategy (str): Strategy for categorical missing values
        """
        self.num_impute_strategy = num_impute_strategy
        self.cat_impute_strategy = cat_impute_strategy

    def fit(self, X, y=None):
        """
        Fit transformer on input data.

        - Identifies feature types
        - Configures feature scaling
        - Sets up encoding
        - Fits imputation strategies

        Parameters:
            X (pd.DataFrame): Input features
            y (pd.Series, optional): Target variable, not used

        Returns:
            CustomTransformer: Fitted transformer
        """
        self.num_features = X.select_dtypes(include=np.number).columns.tolist()
        self.cat_features = X.select_dtypes(exclude=np.number).columns.tolist()

        if self.num_features:
            self.num_transformer = Pipeline(steps=[
                ('imputer', SimpleImputer(strategy=self.num_impute_strategy)),
                ('scaler', StandardScaler())
            ])
            self.num_transformer.fit(X[self.num_features])

        if self.cat_features:
            self.cat_transformer = Pipeline(steps=[
                ('imputer', SimpleImputer(strategy=self.cat_impute_strategy)),
                ('encoder', OneHotEncoder(handle_unknown='ignore'))
            ])
            self.cat_transformer.fit(X[self.cat_features])

        return self

    def get_transformed_cat_cols(self):
        """
        Get transformed categorical column names.

        - Creates names after one-hot encoding
        - Combines category with encoded values

        Returns:
            List[str]: One-hot encoded column names
        """
        cat_cols = []
        cats = self.cat_features
        cat_values = self.cat_transformer['encoder'].categories_
        for cat, values in zip(cats, cat_values):
            cat_cols += [f'{cat}_{value}' for value in values]

        return cat_cols

    def transform(self, X):
        """
        Transform input data.

        - Applies fitted scaling
        - Applies fitted encoding
        - Handles numeric and categorical features

        Parameters:
            X (pd.DataFrame): Input features

        Returns:
            pd.DataFrame: Transformed data
        """
        X_transformed = pd.DataFrame()

        if self.num_features:
            transformed_num_data = self.num_transformer.transform(X[self.num_features])
            X_transformed[self.num_features] = transformed_num_data

        if self.cat_features:
            transformed_cat_data = self.cat_transformer.transform(X[self.cat_features]).toarray()
            self.transformed_cat_cols = self.get_transformed_cat_cols()
            transformed_cat_df = pd.DataFrame(transformed_cat_data, columns=self.transformed_cat_cols)
            X_transformed = pd.concat([X_transformed, transformed_cat_df], axis=1)

        X_transformed.index = X.index

        return X_transformed

    def fit_transform(self, X, y=None):
        """
        Fit and transform input data.

        - Fits transformer to data
        - Applies transformation
        - Combines both operations

        Parameters:
            X (pd.DataFrame): Input features
            y (pd.Series, optional): Target variable, not used

        Returns:
            pd.DataFrame: Transformed data
        """
        self.fit(X, y)
        return self.transform(X)
```

# **自定义预处理器的轻松切换**

就是这样：一个新的预处理器，它 1）更加可定制，2）能够处理数值特征和类别特征。让我们用它定义一个ML管道实例。

```py
# Define a PreProcessor (V2) instance while specifying impute strategy
preprocessor = PreProcessor_v2(
    num_impute_strategy = 'mean'
)
# Define an ML Pipeline instance with this preprocessor
ml_pipeline = ML_PIPELINE(
    model = xgb.XGBClassifier(), # switch ML algorithms
    preprocessor = PreProcessor # switch pre-processors 
)
```

让我们用另一个包含数值特征和类别特征的合成数据集测试这个新的ML管道实例。

```py
# add missings
np.random.seed(42) 
missing_rate = 0.20 
n_missing = int(np.floor(missing_rate * X.size))
rows = np.random.randint(0, X.shape[0], n_missing)
cols = np.random.randint(0, X.shape[1], n_missing)
X.values[rows, cols] = np.nan
actual_missing_rate = X.isna().sum().sum() / X.size
print(f"Target missing rate: {missing_rate:.2%}")
print(f"Actual missing rate: {actual_missing_rate:.2%}")

# change X['inf_1] to categorical
percentiles = [0, 0.1, 0.5, 0.9, 1]
labels = ['bottom', 'lower-mid', 'upper-mid', 'top']
X['inf_1'] = pd.qcut(X['inf_1'], q=percentiles, labels=labels)
```

就是这样——这个ML管道在新数据上运行顺利。然而，正如预期的那样，如果我们用之前的预处理器定义ML管道，然后在这个数据集上运行它，我们将遇到错误，因为之前的预处理器并没有设计来处理类别特征。

```py
# create an ML pipeline instance with PreProcessor v1
ml_pipeline = ML_PIPELINE(
    model = lgb.LGBMClassifier(verbose = -1), 
    preprocessor = PreProcessor()
)

try:
    ml_pipeline.fit(X_train, y_train)
except Exception as e:
    print(f"Error: {e}")
```

```py
Error: Cannot use median strategy with non-numeric data:
could not convert string to float: 'lower-mid'
```

# 可解释的ML管道的好处

在ML管道中添加解释器在多个方面都非常有帮助：

1.  **模型选择**：通过评估模型推理的合理性，它有助于我们选择最佳模型。两个算法在像AUC或精度这样的指标上可能表现相似，但它们依赖的关键特征可能不同。与领域专家一起回顾模型的推理，讨论在这种情况下哪个模型更合理是一个好主意。

1.  **故障排除**：一种有助于模型改进的策略是分析错误背后的推理。例如，在分类问题中，我们可以识别出模型最有信心的假阳性（即预测的可能性最高），并调查推理中出了什么问题，哪些关键特征导致了错误。

1.  **模型监控**：除了数据漂移和性能指标等典型监控元素外，监控模型推理同样具有重要意义。如果生产中驱动模型决策的关键特征发生了显著变化，我希望能够收到警报。

1.  **模型实现**：在某些场景中，提供模型推理和模型预测的结合对于最终用户来说是非常有益的。例如，为了帮助客户服务人员最有效地挽留流失客户，我们可以提供流失评分以及贡献该评分的客户特征。

# 将解释器添加到机器学习管道中

因为我们的机器学习管道是算法无关的，因此解释器也必须能够跨算法工作。

SHAP（Shapley加性解释）值是我们目的的理想选择，因为它们基于博弈论提供理论上稳健的解释。它们设计上能够在各种算法中一致工作，包括基于树的和非基于树的模型，对于后者会有一些近似。此外，SHAP还提供丰富的可视化功能，并被广泛认为是行业标准。

在下面的笔记本中，我深入探讨了SHAP在各种机器学习算法中的实现的相似性与差异。

+   [SHAP用于回归模型](https://github.com/MenaWANG/ML_toy_examples/blob/main/explain%20models/shap_basic_regression.ipynb)

+   [SHAP用于XGBoost分类器](https://github.com/MenaWANG/ML_toy_examples/blob/main/explain%20models/shap_XGB_classification.ipynb)

+   [SHAP用于随机森林分类器](https://github.com/MenaWANG/ML_toy_examples/blob/main/explain%20models/shap_basic_RF_classification.ipynb)

+   [SHAP用于LightGBM分类器](https://github.com/MenaWANG/ML_toy_examples/blob/main/explain%20models/shap_lightgbm_classification.ipynb)

要为我们的机器学习管道创建一个通用的解释器，需要解决的关键差异是

> ***1\. 模型是否被*** `***shap.Explainer***` ***直接支持***

特定模型的SHAP解释器比模型无关的解释器更高效。因此，我们在这里采用的方法是

+   首先尝试使用直接的SHAP解释器来适应模型类型，

+   如果这失败了，则回退到使用predict函数的模型无关解释器。

> ***2\. SHAP值的形状***

对于二分类问题，SHAP值可以有两种格式/形状。

+   **格式 1**：仅显示对正类的影响

```py
shape = (n_samples, n_features) # 2d array
```

+   **格式 2**：显示对两个类别的影响

```py
shape = (n_samples, n_features, n_classes) # 3d array
```

+   以下的解释器实现总是展示对正类的影响。当SHAP值中同时有正类和负类的影响时，它会选择正类的影响。

请参见下面的代码，了解上述方法的实现。

```py
class ML_PIPELINE(mlflow.pyfunc.PythonModel):
    """
    Custom ML pipeline for classification and regression.

    - Works with scikit-learn compatible models
    - Handles data preprocessing
    - Manages model training and predictions
    - Provide global and local model explanation
    - Compatible with MLflow tracking
    - Supports MLflow deployment

    Attributes:
        model (BaseEstimator or None): A scikit-learn compatible model instance
        preprocessor (Any or None): Data preprocessing pipeline
        config (Any or None): Optional config for model settings 
        task(str): Type of ML task ('classification' or 'regression')
        both_class (bool): Whether SHAP values include both classes
        shap_values (shap.Explanation): SHAP values for model explanation
        X_explain (pd.DataFrame): Processed features for SHAP explanation
    """

    # ------- same code as above ---------

    def explain_model(self,X):
        """
        Generate SHAP values and plots for model interpretation. 
        This method:
        1\. Transforms the input data using the fitted preprocessor
        2\. Creates a SHAP explainer appropriate for the model type
        3\. Calculates SHAP values for feature importance
        4\. Generates a summary plot of feature importance

        Parameters:
            X : pd.DataFrame
                Input features to generate explanations for. 

        Returns: None
            The method stores the following attributes in the class:
            - self.X_explain : pd.DataFrame
                Transformed data with original numeric values for interpretation
            - self.shap_values : shap.Explanation
                SHAP values for each prediction
            - self.both_class : bool
                Whether the model outputs probabilities for both classes          
        """
        X_transformed = self.preprocessor.transform(X.copy())
        self.X_explain = X_transformed.copy()
        # get pre-transformed values for numeric features
        self.X_explain[self.preprocessor.num_features] = X[self.preprocessor.num_features]
        self.X_explain.reset_index(drop=True)
        try:
            # Attempt to create an explainer that directly supports the model
            explainer = shap.Explainer(self.model)
        except:
            # Fallback for models or shap versions where direct support may be limited
            explainer = shap.Explainer(self.model.predict, X_transformed)
        self.shap_values = explainer(X_transformed)  

        # get the shape of shap values and extract accordingly
        self.both_class = len(self.shap_values.values.shape) == 3
        if self.both_class:
            shap.summary_plot(self.shap_values[:,:,1])
        elif self.both_class == False:
            shap.summary_plot(self.shap_values)

    def explain_case(self,n):
        """
        Generate SHAP waterfall plot for one specific case.

        - Shows feature contributions
        - Starts from base value
        - Ends at final prediction
        - Shows original feature values for better interpretability

        Parameters:
            n (int): Case index (1-based)
                     e.g., n=1 explains the first case.

        Returns:
            None: Displays SHAP waterfall plot

        Notes:
            - Requires explain_model() first
            - Shows positive class for binary tasks
        """
        if self.shap_values is None:
            print("""
                  Please explain model first by running
                  `explain_model()` using a selected dataset
                  """)
        else:
            self.shap_values.data = self.X_explain
            if self.both_class:
                shap.plots.waterfall(self.shap_values[:,:,1][n-1])
            elif self.both_class == False:
                shap.plots.waterfall(self.shap_values[n-1]) 
```

现在，更新后的机器学习管道实例可以通过一行代码为你创建解释性图表。😎

![](../Images/130400a4a9c80519ff731a20d3ede222.png)

用于模型全局解释的SHAP图

![](../Images/b20822a6bb527748b1849d31e4cb83d0.png)

用于特定案例局部解释的SHAP图

# **记录并使用模型**

当然，你可以使用`mlflow`记录训练好的机器学习管道，并享受所有关于模型部署和可重复性的元数据。在下面的截图中，你可以看到，除了pickle保存的`pyfunc`模型本身，Python环境、指标和超参数都已经在下面的几行代码中记录下来了。想了解更多，请参考我之前关于`mlflow.pyfunc`的文章，链接已在文中提到。

```py
# Log the model with MLflow
with mlflow.start_run() as run:

    # Log the custom model with auto-captured conda environment
    model_info = mlflow.pyfunc.log_model(
        artifact_path="model",
        python_model=ml_pipeline,
        conda_env=mlflow.sklearn.get_default_conda_env()
    )
    # Log model parameters
    mlflow.log_params(ml_pipeline.model.get_params())

    # Log metrics
    mlflow.log_metric("rmse", rmse)

    # Get the run ID
    run_id = run.info.run_id
```

![](../Images/d1ce888009587f800ff1a165eb1fb61a.png)

使用mlflow记录丰富的模型元数据和工件

# **结论与下一步**

就是这样，一个通用且可解释的机器学习管道，适用于分类和回归算法。拿走代码并扩展它以适应你的使用案例。🤗 如果你觉得这个有用，请给我一个掌声 👏🥰

为了进一步推进`mlflow.pyfunc`系列的旅程，以下是我正在考虑的一些话题。欢迎留言告诉我你希望看到哪些内容。🥰

+   特征选择

+   超参数调优

+   如果不选择在现成算法中*挑选*一个，而是决定*集成*多个算法或拥有高度定制的解决方案，他们依然可以享受通用模型表示和通过`mlflow.pyfunc`的无缝迁移。

敬请关注并在[Medium](https://menawang.medium.com/)上关注我。😁

💼[LinkedIn](https://www.linkedin.com/in/mena-ning-wang/) | 😺[GitHub](https://github.com/MenaWANG) | 🕊️[Twitter/X](https://x.com/mena_wang)

除非另有说明，所有图片均由作者提供。
