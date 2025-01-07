# 最小可行机器学习模型（MLE）

> 原文：[https://towardsdatascience.com/minimum-viable-mle-306877dd6030?source=collection_archive---------9-----------------------#2024-10-31](https://towardsdatascience.com/minimum-viable-mle-306877dd6030?source=collection_archive---------9-----------------------#2024-10-31)

## 构建一个最小化的生产就绪情感分析模型

[](https://medium.com/@lenixc210?source=post_page---byline--306877dd6030--------------------------------)[![Lenix Carter](../Images/d25c86c00d6b2ee64b70cae8297fd761.png)](https://medium.com/@lenixc210?source=post_page---byline--306877dd6030--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--306877dd6030--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--306877dd6030--------------------------------) [Lenix Carter](https://medium.com/@lenixc210?source=post_page---byline--306877dd6030--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--306877dd6030--------------------------------) ·阅读时长7分钟·2024年10月31日

--

![](../Images/27789732b7a4449b4ff94b4e82876275.png)

图片由[Stephen Dawson](https://unsplash.com/@dawson2406?utm_source=medium&utm_medium=referral)提供，来自[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# 什么是生产就绪模型？

我们经常听到“生产化的机器学习”，但要让模型在实际应用中茁壮成长究竟意味着什么呢？在生产中，许多因素都影响并有助于机器学习模型的效能。为了本文的目的，我们将重点关注其中的五个因素。

+   **可复现性**

+   **监控**

+   **测试**

+   **自动化**

+   **版本控制**

## 提供推断

构建生产就绪机器学习模型最重要的部分是能够访问它。

为此，我们构建了一个FastAPI客户端，用于提供情感分析的响应。我们利用Pydantic来确保输入和输出的结构化。我们使用的模型是来自Huggingface的Transformers库中的基本情感分析管道，这使我们能够使用预训练模型开始进行测试。

```py
# Filename: main.py
from fastapi import FastAPI
from pydantic import BaseModel
from transformers import pipeline

app = FastAPI()
classifier = pipeline("sentiment-analysis")

class TextInput(BaseModel):
    text: str

class SentimentOutput(BaseModel):
    text: str
    sentiment: str
    score: float

@app.post("/predict", response_model=SentimentOutput)
async def predict_sentiment(input_data: TextInput):
    result = classifier(input_data.text)[0]
    return SentimentOutput(
        text=input_data.text,
        sentiment=result["label"],
        score=result["score"]
    )
```

为了确保我们的工作是可复现的，我们可以使用requirements.txt文件和pip。

```py
# Filename: requirements.txt
# Note: This has all required packages for the final result. 

fastapi==0.68.1
uvicorn==0.15.0
transformers==4.30.0
torch==2.0.0
pydantic==1.10.0
numpy==1.24.3
sentencepiece==0.1.99
protobuf==3.20.3
prometheus-client==0.17.1
```

要安装此项，请初始化[您的文件中的venv](https://packaging.python.org/en/latest/guides/installing-using-pip-and-virtual-environments/)并运行：`pip install -r requirements.txt`。

要托管此API，只需运行：`uvicorn main:app --reload`。

现在您拥有一个可以通过以下方式查询的API：

```py
curl -X POST "http://localhost:8000/predict" \
  -H "Content-Type: application/json" \
  -d '{"text": "I love using FastAPI!"}'
```

或者您想要的任何API工具（即[Postman](https://www.postman.com/)）。您应该能收到一个返回结果，其中包括文本查询、预测的情感以及预测的置信度。

我们稍后将使用 GitHub 进行 CI/CD，因此我建议在此目录中[初始化并使用 git](https://git-scm.com/docs/gittutorial)。

我们现在有一个本地托管的机器学习推理 API。

## 进一步提高可复现性

为了让我们的代码能够更一致地执行，我们将使用 Docker。Docker 模拟了一个轻量级的环境，允许应用程序在隔离的容器中运行，类似于虚拟机。这种隔离确保应用程序能够在任何安装了 Docker 的计算机上稳定执行，而不受底层系统的影响。

首先，[为你的操作系统设置 Docker](https://docs.docker.com/desktop/)。

```py
# Filename: Dockerfile

# Use the official Python 3.9 slim image as the base
FROM python:3.9-slim

# Set the working directory inside the container to /app
WORKDIR /app

# Copy the requirements.txt file to the working directory
COPY requirements.txt .

# Install the Python dependencies listed in requirements.txt
RUN pip install -r requirements.txt

# Copy the main application file (main.py) to the working directory
COPY main.py .

# Define the command to run the FastAPI application with Uvicorn
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
```

此时，你应该拥有如下的目录结构。

```py
your-project/
├── Dockerfile
├── requirements.txt
└── main.py
```

现在，你可以构建镜像并使用以下命令运行这个 API：

```py
# Build the Docker image
docker build -t sentiment-api .

# Run the container
docker run -p 8000:8000 sentiment-api
```

你现在应该能够像之前一样进行查询。

```py
curl -X POST "http://localhost:8000/predict" \
  -H "Content-Type: application/json" \
  -d '{"text": "I love using FastAPI!"}'
```

我们现在拥有一个容器化的、本地托管的机器学习推理 API。

## 添加基础监控

在机器学习应用中，监控对于理解模型性能以及确保其达到预期的准确性和效率至关重要。像[Prometheus](https://prometheus.io/docs/tutorials/getting_started/)这样的工具帮助跟踪诸如预测延迟、请求计数和模型输出分布等指标，使你能够识别诸如模型漂移或资源瓶颈等问题。这种主动的方法确保你的机器学习模型随着时间的推移保持有效，并能够适应不断变化的数据或使用模式。在我们的案例中，我们专注于预测时间、请求和收集查询信息。

```py
from fastapi import FastAPI
from pydantic import BaseModel
from transformers import pipeline
from prometheus_client import Counter, Histogram, start_http_server
import time

# Start prometheus metrics server on port 8001
start_http_server(8001)

app = FastAPI()

# Metrics
PREDICTION_TIME = Histogram('prediction_duration_seconds', 'Time spent processing prediction')
REQUESTS = Counter('prediction_requests_total', 'Total requests')
SENTIMENT_SCORE = Histogram('sentiment_score', 'Histogram of sentiment scores', buckets=[0.0, 0.25, 0.5, 0.75, 1.0])

class TextInput(BaseModel):
    text: str

class SentimentOutput(BaseModel):
    text: str
    sentiment: str
    score: float

@app.post("/predict", response_model=SentimentOutput)
async def predict_sentiment(input_data: TextInput):
    REQUESTS.inc()
    start_time = time.time()

    result = classifier(input_data.text)[0]

    score = result["score"]
    SENTIMENT_SCORE.observe(score)  # Record the sentiment score

    PREDICTION_TIME.observe(time.time() - start_time)

    return SentimentOutput(
        text=input_data.text,
        sentiment=result["label"],
        score=score
    )
```

## 使用自定义模型

尽管构建和微调模型的过程并不是本项目的目的，但理解如何将模型添加到此过程中非常重要。

```py
# Filename: train.py

import torch
from transformers import AutoTokenizer, AutoModelForSequenceClassification
from datasets import load_dataset
from torch.utils.data import DataLoader

def train_model():
    # Load dataset
    full_dataset = load_dataset("stanfordnlp/imdb", split="train")
    dataset = full_dataset.shuffle(seed=42).select(range(10000))

    model_name = "distilbert-base-uncased"
    tokenizer = AutoTokenizer.from_pretrained(model_name)
    model = AutoModelForSequenceClassification.from_pretrained(model_name, num_labels=2)

    optimizer = torch.optim.AdamW(model.parameters(), lr=2e-5)

    # Use GPU if available
    device = torch.device("cuda" if torch.cuda.is_available() else "cpu")
    model.to(device)

    model.train()

    # Create a DataLoader for batching
    dataloader = DataLoader(dataset, batch_size=8, shuffle=True)

    # Training loop
    num_epochs = 3  # Set the number of epochs
    for epoch in range(num_epochs):
        total_loss = 0
        for batch in dataloader:
            inputs = tokenizer(batch["text"], truncation=True, padding=True, return_tensors="pt", max_length=512).to(device)
            labels = torch.tensor(batch["label"]).to(device)

            optimizer.zero_grad()
            outputs = model(**inputs, labels=labels)
            loss = outputs.loss

            loss.backward()
            optimizer.step()
            total_loss += loss.item()

        avg_loss = total_loss / len(dataloader)
        print(f"Epoch {epoch + 1}/{num_epochs}, Loss: {avg_loss:.4f}")

    # Save the model
    model.save_pretrained("./model/")
    tokenizer.save_pretrained("./model/")

    # Test the model with sample sentences
    test_sentences = [
        "This movie was fantastic!",
        "I absolutely hated this film.",
        "It was just okay, not great.",
        "An absolute masterpiece!",
        "Waste of time!",
        "A beautiful story and well acted.",
        "Not my type of movie.",
        "It could have been better.",
        "A thrilling adventure from start to finish!",
        "Very disappointing."
    ]

    # Switch model to evaluation mode
    model.eval()

    # Prepare tokenizer for test inputs
    inputs = tokenizer(test_sentences, truncation=True, padding=True, return_tensors="pt", max_length=512).to(device)

    with torch.no_grad():
        outputs = model(**inputs)
        predictions = torch.argmax(outputs.logits, dim=1)

    # Print predictions
    for sentence, prediction in zip(test_sentences, predictions):
        sentiment = "positive" if prediction.item() == 1 else "negative"
        print(f"Input: \"{sentence}\" -> Predicted sentiment: {sentiment}")

# Call the function to train the model and test it
train_model()
```

为了确保我们能够查询我们训练的新模型，我们需要更新一些现有的文件。例如，在`main.py`中，我们现在使用来自`./model`的模型，并将其加载为预训练模型。此外，为了对比，我们现在有两个可用的端点，`/predict/naive`和`predict/trained`。

```py
# Filename: main.py

from fastapi import FastAPI
from pydantic import BaseModel
from transformers import AutoModelForSequenceClassification, AutoTokenizer
from transformers import pipeline
from prometheus_client import Counter, Histogram, start_http_server
import time

# Start prometheus metrics server on port 8001
start_http_server(8001)

app = FastAPI()

# Load the trained model and tokenizer from the local directory
model_path = "./model"  # Path to your saved model
tokenizer = AutoTokenizer.from_pretrained(model_path)
trained_model = AutoModelForSequenceClassification.from_pretrained(model_path)

# Create pipelines
naive_classifier = pipeline("sentiment-analysis", device=-1)
trained_classifier = pipeline("sentiment-analysis", model=trained_model, tokenizer=tokenizer, device=-1)

# Metrics
PREDICTION_TIME = Histogram('prediction_duration_seconds', 'Time spent processing prediction')
REQUESTS = Counter('prediction_requests_total', 'Total requests')
SENTIMENT_SCORE = Histogram('sentiment_score', 'Histogram of sentiment scores', buckets=[0.0, 0.25, 0.5, 0.75, 1.0])

class TextInput(BaseModel):
    text: str

class SentimentOutput(BaseModel):
    text: str
    sentiment: str
    score: float

@app.post("/predict/naive", response_model=SentimentOutput)
async def predict_naive_sentiment(input_data: TextInput):
    REQUESTS.inc()
    start_time = time.time()

    result = naive_classifier(input_data.text)[0]

    score = result["score"]
    SENTIMENT_SCORE.observe(score)  # Record the sentiment score

    PREDICTION_TIME.observe(time.time() - start_time)

    return SentimentOutput(
        text=input_data.text,
        sentiment=result["label"],
        score=score
    )

@app.post("/predict/trained", response_model=SentimentOutput)
async def predict_trained_sentiment(input_data: TextInput):
    REQUESTS.inc()
    start_time = time.time()

    result = trained_classifier(input_data.text)[0]

    score = result["score"]
    SENTIMENT_SCORE.observe(score)  # Record the sentiment score
```

我们还必须更新我们的 Dockerfile，以包含我们的模型文件。

```py
# Filename: Dockerfile
FROM python:3.9-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install -r requirements.txt

COPY main.py .
COPY ./model ./model

CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
```

重要的是，如果你使用 git，确保将`pytorch_model.bin`文件添加到[git lfs](https://git-lfs.com/)，这样你就可以推送到 GitHub。git lfs 允许你对非常大的文件使用版本控制。

## 添加测试和 CI/CD

CI/CD 和测试通过确保代码更改自动集成、测试和部署，从而简化了机器学习模型的部署，这减少了错误的风险并提高了模型的可靠性。这个过程促进了持续改进和更快的迭代周期，使团队能够更高效地交付高质量、准备投入生产的模型。首先，我们创建了两个非常基础的测试，确保我们的模型表现得足够好。

```py
# Filename: test_model.py

import pytest
from fastapi.testclient import TestClient
from main import app

client = TestClient(app)

def test_positive_sentiment():
    response = client.post(
        "/predict/trained",
        json={"text": "This is amazing!"}
    )
    assert response.status_code == 200
    data = response.json()
    assert data["sentiment"] == "LABEL_1"
    assert data["score"] > 0.5

def test_negative_sentiment():
    response = client.post(
        "/predict/trained",
        json={"text": "This is terrible!"}
    )
    assert response.status_code == 200
    data = response.json()
    assert data["sentiment"] == "LABEL_0"
    assert data["score"] < 0.5
```

要测试你的代码，你可以在端点运行时简单地执行`pytest`或`python -m pytest`。

然而，当推送到 GitHub 时，我们将添加自动化测试 CI/CD（持续集成与持续交付）。

```py
# Filename: .github/workflows/ci_cd.yml

name: CI/CD

on: [push]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v2
        with:
          lfs: true

      - name: Set up Python
        uses: actions/setup-python@v2
        with:
          python-version: '3.9'

      - name: Install dependencies
        run: |
          pip install -r requirements.txt
          pip install pytest httpx

      - name: Run tests
        run: pytest
```

我们最终的项目结构应如下所示。

```py
sentiment-analysis-project/
├── .github/
│   └── workflows/
│       └── ci_cd.yml
├── test_model.py
├── main.py
├── Dockerfile
├── requirements.txt
└── train.py
```

现在，每当我们推送到 GitHub 时，它将运行一个自动化过程，检出代码，设置 Python 3.9 环境，安装依赖，并使用 pytest 运行我们的测试。

# 结论

在这个项目中，我们开发了一个生产就绪的情感分析 API，突出了部署机器学习模型的关键方面。虽然它并不涵盖该领域的每个方面，但它提供了涉及这一过程的基本任务的代表性样本。通过检查这些组件，我希望能澄清你可能遇到过的概念，但不确定它们如何在实际环境中配合工作。
