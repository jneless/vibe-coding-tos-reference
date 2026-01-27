# 火山方舟 Embedding
* 火山方舟大模型服务
* 提供两种SDK接入方式
    * OPENAI SDK（兼容）
    * 火山方舟 SDK
* 使用类似 openai 风格的 token 认证鉴权
* 可以在请求中携带，通常在 env 的 ARK_API_KEY 中

## SDK 安装

火山方舟支持 **Python、Go、Java** 三种官方 SDK，并原生兼容 **OpenAI 协议**。

### 1. 官方 SDK 快速概览

| 语言 | 最低版本要求 | 主要安装/管理命令 | 备注 |
| --- | --- | --- | --- |
| **Python** | Python 3.7+ | `pip install 'volcengine-python-sdk[ark]'` | 支持 `-U` 参数升级 |
| **Go** | Go 1.18+ | `go get -u github.com/volcengine/volcengine-go-sdk` | 依赖 `go mod` 管理 |
| **Java** | JDK 1.8+ | Maven (`pom.xml`) 或 Gradle (`build.gradle`) | 仅限服务端，不支持 Android |

---

## openai 兼容

```
pip install --upgrade "openai>=1.0"
```
* 快速开始
```
from openai import OpenAI
import os

client = OpenAI(   
    # The base URL for model invocation . 
    base_url="https://ark.cn-beijing.volces.com/api/v3",   
    # 环境变量中配置您的API Key 
    api_key=os.environ.get("ARK_API_KEY"), 
)

completion = client.chat.completions.create(
    # Replace with Model ID . 
    model="doubao-seed-1-6-251015", 
    messages = [
        {"role": "user", "content": "Hello"},
    ],
)
print(completion.choices[0].message.content)
```
* 设置额外字段
* 传入OpenAI SDK中不支持的字段，可以通过 extra_body 字典传入，如开关模型是否深度思考的 thinking 字段。

```
from openai import OpenAI
import os

client = OpenAI(   
    # The base URL for model invocation . 
    base_url="https://ark.cn-beijing.volces.com/api/v3",  
    # 环境变量中配置您的API Key 
    api_key=os.environ.get("ARK_API_KEY"), 
)

completion = client.chat.completions.create(
    # Replace with Model ID .
    model="doubao-seed-1-6-251015", 
    messages = [
        {"role": "user", "content": "Hello"},
    ],
    extra_body={
         "thinking": {
             "type": "disabled", # 不使用深度思考能力
             # "type": "enabled", # 使用深度思考能力
         }
     }
)
print(completion.choices[0].message.content)
```

* 设置自定义header
* 可以用于传递额外信息，如配置 ID来串联日志，使能数据加密能力。

```
from openai import OpenAI
import os

client = OpenAI(   
    # The base URL for model invocation . 
    base_url="https://ark.cn-beijing.volces.com/api/v3",  
    # 环境变量中配置您的API Key 
    api_key=os.environ.get("ARK_API_KEY"), 
)

completion = client.chat.completions.create(
    # Replace with Model ID .
    model="doubao-seed-1-6-251015", 
    messages = [
        {"role": "user", "content": "Hello"},
    ],
    # 自定义request id
    extra_headers={"X-Client-Request-Id": "202406251728190000B7EA7A9648AC08D9"}
)
print(completion.choices[0].message.content)
```

* 文本向量化 Embedding 
* 注意:多模态向量化能力模型不支持 OpenAI API ，如需使用请使用 方舟 SDK，详情请参考多模态向量化。

```
from openai import OpenAI
import os

client = OpenAI(   
    # The base URL for model invocation . 
    base_url="https://ark.cn-beijing.volces.com/api/v3",  
    # 环境变量中配置您的API Key 
    api_key=os.environ.get("ARK_API_KEY"), 
)

resp = client.embeddings.create(
    # Replace with Model ID .
    model="doubao-embedding-large-text-240915", 
    input=["Nice day."]
)
print(resp)
```

* LangChain OpenAI SDK
* 安装 LangChain OpenAI SDK:pip install langchain-openai

```
from langchain_openai import ChatOpenAI
from langchain_core.prompts import PromptTemplate
import os

llm = ChatOpenAI(
    # 环境变量中配置您的API Key       
    openai_api_key=os.environ.get("ARK_API_KEY"), 
    # The base URL for model invocation
    openai_api_base="https://ark.cn-beijing.volces.com/api/v3",   
    # Replace with Model ID
    model="doubao-seed-1-6-251015", 
)

template = """Question: {question}

Answer: Let's think step by step."""

prompt = PromptTemplate.from_template(template)

question = "What NFL team won the Super Bowl in the year Justin Beiber was born?"

llm_chain = prompt | llm

print(llm_chain.invoke(question))
```


## SDK 常见使用示例

* 当您使用官方 SDK 时，设置自定义header等典型的使用，可以参考本说明。
* 设置超时&重试次数，超时设置（timeout）：适用于对响应时间较长的场景（如深度思考模型回复问题时间较长），避免请求时间超时导致服务中断，这里推荐30分钟（1800秒）。
* Python SDK：默认 60秒 连接超时（建立网络连接的最大等待时间），600 秒 socket 超时（连接建立后的数据传输超时）。
* Java SDK：默认 60秒 连接超时，600 秒 socket 超时。
* Go SDK：默认 600s 端到端的总超时（从请求发起至响应接收的总超时）。
* 重试次数（max_retries）：适用于网络不稳定场景，自动重试因瞬时故障（如网络波动）失败的请求，默认2次，即请求失败默认会再重试2次，您可以按需配置。

```
import os
# 通过命令安装方舟SDK pip install 'volcengine-python-sdk[ark]' .
from volcenginesdkarkruntime import Ark 

client = Ark(
    api_key=os.environ.get("ARK_API_KEY"),
    # 设置服务响应超时时间，单位秒，推荐1800秒及以上
    timeout=1800,
    # 设置重试次数
    max_retries=2,
    # The base URL for model invocation .
    base_url="https://ark.cn-beijing.volces.com/api/v3",
)
```

* 使用Access Key鉴权
* 当需要通过火山引擎云服务的标准鉴权体系（Access Key/Secret Key）认证时使用。
* 使用 Access Key 鉴权的实现原理是通过接口 GetApiKey 获取临时 API Key 进行鉴权，此接口限流较低，务必使用单例模式进行请求，避免因限流导致鉴权失败，可参考单例请求
```
import os
# 通过命令安装方舟SDK pip install 'volcengine-python-sdk[ark]' .
from volcenginesdkarkruntime import Ark 
client = Ark(
    ak=os.environ.get("VOLC_ACCESSKEY"),
    sk=os.environ.get("VOLC_SECRETKEY"),
    # The base URL for model invocation .
    base_url="https://ark.cn-beijing.volces.com/api/v3",
)
```
* 设置自定义 header
* 自定义的 header 字段采用键值对（key-value）结构，以对象形式呈现，具体格式示例如下：
* 可以用于传递额外信息，如配置 ID来串联日志，使能数据加密能力。支持该字段的接口有 对话（Chat） API、批量（Chat）API，下面是典型示例。
```
{
    "key1": "value1",
    "key2": "value2",
    ...,
    "keyN": "valueN"
}
```
问题定位
您可以在请求时，为多个请求设置 key 为 X-Client-Request-Id，value 配置为自定义的 ID，并提供 ID 给方舟售后团队，为您串联客户端和服务端日志，方便问题定位。
```
import os
# 通过命令安装方舟SDK pip install 'volcengine-python-sdk[ark]' .
from volcenginesdkarkruntime import Ark 

client = Ark(
    api_key=os.environ.get("ARK_API_KEY"),
    # The base URL for model invocation .
    base_url="https://ark.cn-beijing.volces.com/api/v3",
)

completion = client.chat.completions.create(
    # Replace with Model ID .
    model = "doubao-seed-1-6-251015",
    messages = [
        {"role": "user", "content": "Hello"},
    ],
    # 自定义request id
    extra_headers={"X-Client-Request-Id": "My-Request-Id"}
)
print(completion.choices[0].message.content)
```


## 文本向量化
* 文本字符串转换向量
* 通过调用doubao-embedding-text-240715模型，将输入的文本字符串转换为向量表示，并输出向量维度和前10维数值。
* 注意为获得更好性能，建议文本数量总token不超过4096，或者文本条数不超过4。
```
import os
from volcenginesdkarkruntime import Ark
# 初始化客户端
client = Ark(
    # 从环境变量中读取您的方舟API Key
    api_key=os.environ.get("ARK_API_KEY"),
    base_url="https://ark.cn-beijing.volces.com/api/v3"
)
response = client.embeddings.create(
    model="doubao-embedding-text-240715",
    input="Function Calling 是一种将大模型与外部工具和 API 相连的关键功能",
    encoding_format="float"  
)
# 打印结果
print(f"向量维度: {len(response.data[0].embedding)}")
print(f"前10维向量: {response.data[0].embedding[:10]}")
```

* 输入文本文件逐行转换
* 向量化模型可以基于您上传的文档生成嵌入向量。此处以embedding_text.txt作为示例文件，您可以通过代码对文本文件逐行转化成向量。
```
import os
from volcenginesdkarkruntime import Ark
client = Ark(
    # 从环境变量中读取您的方舟API Key
    api_key=os.environ.get("ARK_API_KEY"),
    base_url="https://ark.cn-beijing.volces.com/api/v3"
)
# 从文件读取文本并生成向量
with open("embedding_text.txt", "r", encoding="utf-8") as f:
    # 按行分割文本（每行作为一个独立输入）
    texts = [line.strip() for line in f if line.strip()]
    
    response = client.embeddings.create(
        model="doubao-embedding-text-240715",
        input=texts,
        encoding_format="float"
    )
# 打印结果
print(f"处理文本数量: {len(response.data)}")
print(f"首个文本向量维度: {len(response.data[0].embedding)}")
```
### 相似度计算
* Doubao-embedding向量间相似度得分可以使用余弦相似度作为计算方式，余弦相似度计算拆分为下面两步：
* 第一步: 请求doubao-embedding接口得到embedding，将embedding向量L2_norm处理;
* 第二步: 对norm处理后的向量进行点积计算得到余弦相似度;

### 向量降维
* 向量化是通过向量来表征文本、图像等非结构化数据的过程，让计算机能明白语言、图像等的含义。其中标注词义的维度是描述向量化后向量中数字的个数。在文本向量化场景，每个维度对应文本的一个特征。
* 维度更多：以更多独立特征标注词语来捕捉语义细节，提升表征精度。但会导致数据量膨胀，带来更高的存储成本（内存/磁盘占用）和计算开销（相似度计算、模型推理耗时）。
* 维度更低：以更少特征标注词语，可能损失部分细节。但数据量缩减，存储效率与计算速度会提升，资源消耗以及成本更低。
* 下列模型支持多种维度，您可通过向量降维，选择合适的维度来向量化文本，平衡“语义精度”、”计算速度“与“资源成本”三者成本。


### 豆包向量化模型 (Doubao Embedding) 规格表

| 模型 ID (Model ID) | 支持维度 | 备注 |
| --- | --- | --- |
| **doubao-embedding-large-text-250515** | 2048 (支持 2048、1024、512、256 降维使用) | 需要 L2 归一化后使用。**L2 归一化**是将向量中每个元素除以该向量的 L2 范数（各元素平方和的平方根），使向量长度为 1，从而消除量纲差异并保留相对大小关系。 |
| **doubao-embedding-large-text-240915** | 4096 (支持 2048、512、1024 降维使用) | 无 |
| **doubao-embedding-text-240715** | 2560 (支持 512、1024、2048 降维使用) | 无 |
| **doubao-embedding-text-240515** | 2048 (支持 512、1024 降维使用) | 无 |

推荐用法：常规降维方式以doubao-embedding-text-240715模型为例，最高维度2560可以压缩到512, 1024, 2048维度存储检索，维度越高越接近最高维度效果。
如何降维度&计算相似度？
降维度: 将embedding接口获取的向量直接截取前dim维度;
计算相似度: 对截取后的embedding做余弦相似度计算;

```
# 降维 + L2_norm
def sliced_norm_l2(vec: List[float], dim=2560) -> List[float]:
    # dim 取值 512,1024,2048
    norm = float(np.linalg.norm(vec[ :dim]))
    return [v / norm for v in vec[ :dim]]
    
# 余弦相似度计算
query_doc_relevance_score_2560d = np.matmul(
    sliced_norm_l2(embeddings[0], 2560), #查询向量
    sliced_norm_l2(embeddings[1], 2560)  #文档向量
)
```
* 针对doubao-embedding-large-text-250515模型，需要降维后L2归一化使用。模型支持多种嵌入维度：[2048、1024、512、256] ，即使在较低维度下性能下降也较小。
* 如何降维度&计算相似度？
* 降维度: 将embedding接口获取的向量直接截取前dim维度;
* 归一化：使用 L2 归一化统一向量长度，确保余弦相似度计算准确。
计算相似度: 对截取后的embedding做余弦相似度计算;
```
def encode(
    client, inputs: List[str], is_query: bool = False, mrl_dim: Optional[int] = None
):
    # 处理查询文本（添加指令模板优化检索性能）
    if is_query:
        inputs = [f"Instruct: Given a web search query...\nQuery: {i}" for i in inputs]  
    # 调用API获取原始向量（未归一化）
    resp = client.embeddings.create(
        model="doubao-embedding-large-text-250515",
        input=inputs,
        encoding_format="float",
    )
    # 转换为张量并降维（截取前mrl_dim维度）
    embedding = torch.tensor([d.embedding for d in resp.data], dtype=torch.bfloat16)
    if mrl_dim is not None:
        assert mrl_dim in [256, 512, 1024, 2048], "仅支持256/512/1024/2048维"
        embedding = embedding[:, :mrl_dim]  
    # 必须执行归一化：L2归一化后才能计算余弦相似度
    embedding = torch.nn.functional.normalize(embedding, dim=1, p=2).float().numpy()
    return embedding
```

### 最佳实践
* 下列程序实现了将查询文本和资料库文本向量匹配的功能。我们以embedding_text.txt的多行文本作为资料库，通过调用Doubao-embedding模型生成文本向量。当程序接收到用户的查询文本时，将其向量化并通过余弦相似度匹配资料库的向量，最终返回最相关的前3条文本及对应相似度分数。
#### 第一步：客户端初始化
导入所需的库包，并设置 API Key，为后续的数据处理和分析做准备。
```
import os
# 导入火山引擎大模型SDK
from volcenginesdkarkruntime import Ark
# 初始化客户端
client = Ark(
    api_key=os.environ.get("ARK_API_KEY"),
    base_url="https://ark.cn-beijing.volces.com/api/v3"
)
```
#### 第二步：从文件读取文本并生成向量
读取包含文本的embedding_text.txt文件，调用文本向量模型 API 逐行生成文本对应的向量，并将文本和向量保存为 JSON 文件。
```
def generate_and_save_embeddings(file_path="embedding_text.txt", output_path="embeddings.json"):
    with open(file_path, "r", encoding="utf-8") as f:
        texts = [line.strip() for line in f if line.strip()]
    # 调用向量化API
    response = client.embeddings.create(
        model="doubao-embedding-text-240715",
        input=texts,
        encoding_format="float"
    )
    # 构建结果并保存
    results = [{"text": text, "embedding": data.embedding} 
              for text, data in zip(texts, response.data)]
    
    with open(output_path, "w", encoding="utf-8") as f:
        json.dump(results, f)
    
    print(f"已生成并保存 {len(results)} 条向量至 {output_path}")
    return results
```
#### 第三步：加载预计算的向量数据
从 JSON 文件加载预计算的文本向量数据，为后续的相似度计算做准备。
```
def load_embeddings(file_path="embeddings.json"):
    with open(file_path, "r", encoding="utf-8") as f:
        return json.load(f)
```
#### 第四步：定义计算余弦相似度函数和搜索相似文本函数
利用余弦相似度来度量文本之间的相似性，实现了一个基于内容的文本搜索功能。
用户可以通过输入查询文本，检索与该查询文本最相关的文本。
```
# 定义计算余弦相似度函数
def cosine_similarity(a, b):
    a = np.array(a)
    b = np.array(b)
    return np.dot(a, b) / (np.linalg.norm(a) * np.linalg.norm(b))
    
# 定义search_similar_text 函数：搜索与查询文本最相似的前N条文本
def search_similar_text(query_text, embeddings, top_n=3):
    # 生成查询文本的向量
    query_response = client.embeddings.create(
        model="doubao-embedding-text-240715",
        input=[query_text],
        encoding_format="float"
    )
    query_embedding = query_response.data[0].embedding
    # 计算相似度
    for item in embeddings:
        item["similarity"] = cosine_similarity(item["embedding"], query_embedding)
    # 排序并返回结果
    sorted_results = sorted(embeddings, key=lambda x: x["similarity"], reverse=True)
    return sorted_results[:top_n]
```
#### 第五步：测试搜索功能
测试搜索功能，调用 search_similar_text 函数查询与query变量相关的文本，并返回与该查询文本最相关的前 3 条文本及其相似度分数。
```
# 示例：生成向量并搜索
if __name__ == "__main__":
    # 生成或加载向量
    try:
        embeddings = load_embeddings()
        print(f"已加载 {len(embeddings)} 条预计算向量")
    except FileNotFoundError:
        print("未找到预计算向量，将从文件生成...")
        embeddings = generate_and_save_embeddings()
    
    # 执行搜索（示例查询：上下文缓存机制）
    query = "上下文缓存（Context API）是方舟提供的一个高效的缓存机制，旨在为您优化生成式AI在不同交互场景下的性能和成本。"
    results = search_similar_text(query, embeddings, top_n=3)
    
    # 打印结果
    print(f"\n搜索查询: '{query}'")
    for i, result in enumerate(results, 1):
        print(f"\nTop {i} (相似度: {result['similarity']:.4f}):")
        print(f"{result['text'][:200]}...")  # 显示前200个字符
```

### 请求参数 

* model string 必选
您需要调用的模型的 ID （Model ID），开通模型服务，并查询 Model ID 。
您也可通过 Endpoint ID 来调用模型，获得限流、计费类型（前付费/后付费）、运行状态查询、监控、安全等高级能力，可参考获取 Endpoint ID。

* input string / string[] 必选
需要向量化的内容列表，支持中文、英文。输入内容需满足下面条件：
不得超过模型的最大输入 token 数。doubao-embdding 模型，每个列表元素（并非单次请求总数）最大输入token 数为 4096。
不能为空列表，列表的每个成员不能为空字符串。
单条文本以 utf-8 编码，长度不超过 100,000 字节。
为获得更好性能，建议文本数量总token不超过4096，或者文本条数不超过4。

* encoding_format string / null  默认值 float
取值范围： float、base64、null。
表示 embedding 返回的格式。

### 响应参数

id string
本次请求的唯一标识 。

model string
本次请求实际使用的模型名称和版本。

created integer
本次请求创建时间的 Unix 时间戳（秒）。

object string
固定为 list。

data object
本次请求的算法输出内容。

data.index integer
向量的序号，与请求参数 input 列表中的内容顺序对应。

data.embedding float[]
对应内容的向量化结果。

data.object string
固定为 embedding。

usage object
本次请求的 token 用量。

usage.prompt_tokens integer
输入内容 token 数量。

usage.total_tokens integer
本次请求消耗的总 token 数量（输入 + 输出）。


## 多模态向量化
Doubao-embedding-vision 是由字节跳动研发的多模态向量化模型。它能将文本、图片以及视频等混合输入内容转换为统一的向量表示，从而帮助您更高效地处理跨模态数据，实现精准的文搜图、图搜图和图文混合搜索。
当前模型支持以下几种向量输出类型：
稠密向量 (Dense Embedding)：所有版本均默认支持。
稀疏向量 (Sparse Embedding)：从 doubao-embedding-vision-250615 版本起支持，且仅支持文本输入。


### 多模态向量模型 (Vision Embedding)


| 模型版本 | 输入能力 | 稀疏向量 (Sparse) | `instructions` 字段 |
| --- | --- | --- | --- |
| `vision-250328` | 最多 1 文本 + 1 图片 | 不支持 | 不支持 |
| `vision-250615` | 不限数量文本/图片/视频 | 支持（仅限文本输入） | 不支持 |
| `vision-251215` 及后续 | 不限数量文本/图片/视频 | 支持（仅限文本输入） | **支持** |

* 注意:【重要】instructions 字段的配置直接决定模型推理效果。为了显著提升向量表示的精度，您需要根据具体的业务场景来定制该指令。请勿直接使用系统默认值。详情请参考设置 instructions 字段（推荐）。
* 本文档不包含 instructions 用法

* 开启稀疏向量（Sparse Embedding）
* 仅 doubao-embedding-vision-250615 及后续版本支持。稀疏向量仅支持纯文本输入。
    * TOS vectors 不支持稀疏向量，在与 tos vectors 联合使用的场景，请不要使用稀疏向量
* 通过独立的 sparse_embedding 字段控制，示例：
```
{
    "model": "doubao-embedding-vision-251215",
    "input": [
        {
            "type":"text",
            "text":"天很蓝"
        }
    ],
    "sparse_embedding": {
        "type":"enabled"     # 开启稀疏向量，默认值为 "disabled"
    },
    "encoding_format":"float"
}
```

### 设置向量维度 dimensions
向量化是通过向量来表征文本、图像等非结构化数据的过程，让计算机能理解语言、图像等的含义。其中，向量维度是描述向量化后向量中元素的个数（标注词义/图像特征的维度）。在多模态向量化场景，每个维度对应文本的一个特征或者是对应图像的像素、色彩等视觉特征。
doubao-embedding-vision-250615及后续版本支持通过dimensions 参数指定稠密向量的输出维度，对输出的data.embedding 字段生效。
注意
目前仅支持对稠密向量设置维度，稀疏向量（固定维度）不支持。
doubao-embedding-vision-250328 模型不支持此字段，请参考通过编码实现向量降维。
```
{
    "model": "doubao-embedding-vision-250615",
    "input": [
        {
            "type":"text",
            "text":"天很蓝"
        }
    ],
    "dimensions": 1024,    #设置向量维度
    "encoding_format":"float"
}
```

### 图片格式说明
图片传入方式：图片 URL 或图片 Base64 编码。用图片 URL 方式时，需确保图片 URL 可被访问。
图片文件容量：单张图片小于 10 MB。使用 base64 编码，请求中请求体大小不可超过 64 MB。
图片像素说明：模型能够支持尺寸更加灵活的图片，传入图片满足下面条件：图片宽高长度（单位 px）：大于 14。图片像素（宽＊高，单位 px）：小于 3600万。
图片数量说明：doubao-embedding-vision-250615 及后续版本 支持不限数量的视频、文本和图片混合输入。doubao-embedding-vision-250328 模型仅支持最多 1 文本 和 1 图片 输入。

### 最佳实践：多模态相似度匹配示例

第一步：导入库包
导入所需的库包，并设置 API Key，为后续的数据处理和分析做准备。
```
import os
import numpy as np
from volcenginesdkarkruntime import Ark
from sklearn.metrics.pairwise import cosine_similarity
```


第二步：获取向量
定义一个函数将单个文本或图片转换为向量表示。支持两种输入类型：文本和图片 URL。调用doubao-embedding-vision-241215模型获取float格式向量，再转换为numpy数组并展平为一维向量。
```
def get_embedding(input_data, input_type="text"):
    """调用火山引擎API获取单个文本或图片的向量表示"""
    client = Ark(api_key=os.environ.get("ARK_API_KEY"))
    if input_type == "text":
        input_item = {"type": "text", "text": input_data}
    elif input_type == "image_url":
        input_item = {"type": "image_url", "image_url": {"url": input_data}}
    else:
        raise ValueError("输入类型仅支持'text'或'image_url'")
    
    try:
        resp = client.multimodal_embeddings.create(
            model="doubao-embedding-vision-241215",
            encoding_format="float",
            input=[input_item]
        )
        if hasattr(resp, 'data') and hasattr(resp.data, 'embedding'):
            embedding = resp.data['embedding']
            # 确保向量是numpy数组并展平为一维
            embedding = np.array(embedding).flatten()
            return embedding
        else:
            raise ValueError("API响应格式不符合预期，无法获取嵌入向量")
    except Exception as e:
        print(f" 获取向量失败，输入类型: {input_type}, 错误: {str(e)}")
        raise
```


第三步：生成向量库
批量处理图片 URL 列表，生成对应的向量表示，并构建向量库。
```
def generate_image_embeddings(image_urls):
    """批量生成图片向量并构建向量库"""
    print(f"[1/3] 开始生成 {len(image_urls)} 张图片的向量...")
    embeddings = []
    for i, url in enumerate(image_urls):
        try:
            embedding = get_embedding(url, "image_url")
            embeddings.append({
                "image_url": url,
                "embedding": embedding
            })
            print(f" [{i+1}/{len(image_urls)}] 成功: {url}")
        except Exception as e:
            print(f" [{i+1}/{len(image_urls)}] 失败: {url} - {str(e)}")
            continue
    if not embeddings:
        raise ValueError("所有图片向量生成失败")    
    print(f"[2/3] 完成: {len(embeddings)} 个有效向量")
    return embeddings
```


第四步：图片相似度计算
利用余弦相似度来度量文本与图片之间的相似性，实现了一个基于内容的图片搜索功能。用户可以通过输入文本描述，检索与该描述最相关的图片。
```
def search_similar_images(query_embedding, embeddings, top_n=1, query_type="文本"):
    """搜索与查询向量最相似的图片"""
    print(f"\n[3/3] 开始搜索与{query_type}最相似的图片...")
    results = []
    # 确保查询向量是numpy数组且维度正确
    query_vec = np.array(query_embedding).reshape(1, -1)
    for item in embeddings:
        # 确保向量是numpy数组并调整为二维数组用于相似度计算
        item_vec = np.array(item["embedding"]).reshape(1, -1)
        
        similarity = cosine_similarity(query_vec, item_vec)[0][0]
        results.append({
            "image_url": item["image_url"],
            "similarity": similarity
        })
    
    results.sort(key=lambda x: x["similarity"], reverse=True)
    print(f" - 相似度计算完成，共 {len(results)} 个结果")
    return results[:top_n]
```

第五步：示例用法
测试搜索功能，调用generate_image_embeddings函数生成图片向量库，使用文本查询搜索相似图片，并返回最相似的结果。示例代码如下：
```
if __name__ == "__main__":
    image_urls = [
        "https://ark-project.tos-cn-beijing.volces.com/doc_image/Fruit1.jpg",
        "https://ark-project.tos-cn-beijing.volces.com/doc_image/Fruit2.jpg",
        "https://ark-project.tos-cn-beijing.volces.com/doc_image/Fruit3.jpg",
        "https://ark-project.tos-cn-beijing.volces.com/doc_image/Fruit4.jpg",
        "https://ark-project.tos-cn-beijing.volces.com/doc_image/Fruit5.jpg"
    ]
    query_text = "香蕉"
  
    try:
        # 生成图片向量
        image_embs = generate_image_embeddings(image_urls)        
        # 生成文本向量
        text_emb = get_embedding(query_text, "text")
        print(f"文本向量维度: {len(text_emb)}")        
        # 搜索相似图片
        similar_images = search_similar_images(text_emb, image_embs)
        print(f"最相似图片: {similar_images[0]['image_url']}")
        print(f"相似度分数: {similar_images[0]['similarity']:.4f}")
        
    except Exception as e:
        print(f"程序失败: {e}")
```

## 相关技术
### 通过编码实现向量降维
注意 doubao-embedding-vision-250615及后续版本支持设置向量维度 dimensions。
稀疏向量不支持降维。
doubao-embedding-vision-241215向量维度3072维，不支持降维使用
doubao-embedding-vision-250328模型支持最高维度2048可以压缩到1024维度存储检索，维度越高越接近最高维度效果。

### 如何降维度&计算相似度？
降维度: 将embedding接口获取的向量直接截取前dim维度;
计算相似度: 对截取后的embedding做余弦相似度计算;
```
# 降维+ L2_norm
def sliced_norm_l2(vec: List[float], dim=2048) -> List[float]:
    # dim 为1024
    norm = float(np.linalg.norm(vec[ :dim]))
    return [v / norm for v in vec[ :dim]]
    
# 余弦相似度计算
query_doc_relevance_score_2048d = np.matmul(
    sliced_norm_l2(embeddings[0], 2048), #查询向量
    sliced_norm_l2(embeddings[1], 2048)  #文档向量
)
```

### Base64 编码输入
如果你要传入的视频/图片在本地，你可以将这个视频/图片转化为 Base64 编码，然后提交给大模型。下面是一个简单的转换示例代码。
注意
传入 Base64 编码格式时，请遵循以下规则：
传入的是图片：
格式遵循data:image/<图片格式>;base64,<Base64编码>，其中，
图片格式：jpeg、png、gif等，支持的图片格式详细见图片格式说明。
Base64 编码：图片的 Base64 编码。
传入的是视频：
格式遵循data:video/<视频格式>;base64,<Base64编码>，其中，
视频格式：MP4、AVI等，支持的视频格式详细见视频格式说明。
Base64 编码：视频的 Base64 编码。

```
# 定义方法将指定路径图片转为Base64编码
def encode_image(image_path):
  with open(image_path, "rb") as image_file:
    return base64.b64encode(image_file.read()).decode('utf-8')

# 需要传给大模型的图片
image_path = "path_to_your_image.jpg"
# 将图片转为Base64编码
base64_image = encode_image(image_path)
```

转换后，图片的url格式参考如下：
```
    {
        "type": "image_url",
        "image_url": {
            "url":  f"data:image/<IMAGE_FORMAT>;base64,{base64_image}"
        }
    },
```
### 向量批量处理的解决方案
针对 API 仅支持单次传入单张图片的限制，本代码通过异步并发 + 批量分组策略提升图片向量化的批量处理效率：利用 asyncio 创建异步任务，将图片按设定批次分组后并发调用 API；采用 “全组失败回滚” 模式确保每组任务结果一致，支持失败重试，且单独保存成功 / 失败结果（包含向量详情、错误信息和图片输入 URL）。
```
import asyncio
import os
from pathlib import Path
from volcenginesdkarkruntime import AsyncArk

class MultimodalEmbedder:
    """多模态向量化批量处理工具（全组失败模式）"""
    def __init__(self, api_key: str, model: str = "doubao-embedding-vision-241215", 
                 batch_size: int = 10, retries: int = 2):
        self.api_key = api_key
        self.model = model
        self.batch_size = batch_size  # 批量大小配置（可扩展分片逻辑）
        self.retries = retries        # 批量请求重试次数

    async def batch_process(self, items_list):
        """核心：异步批量处理向量任务（全组失败）"""
        # 1. 初始化批量客户端
        async with AsyncArk(max_retries=self.retries) as client:
            # 2. 批量创建异步任务（按输入列表分片）
            batch_tasks = [
                asyncio.create_task(client.multimodal_embeddings.create(model=self.model, input=items))
                for items in items_list
            ]

            try:
                # 3. 批量等待任务完成（任一失败则全组终止）
                batch_results = await asyncio.gather(*batch_tasks)
                return batch_results
            except Exception:
                # 4. 批量清理未完成任务
                for task in batch_tasks:
                    if not task.done():
                        task.cancel()
                raise  # 抛出异常，保持全组失败语义

    def batch_save(self, batch_results, output_dir: str = "embedding_results"):
        """核心：批量保存向量结果"""
        # 1. 初始化输出目录
        Path(output_dir).mkdir(exist_ok=True)
        # 2. 批量写入结果文件
        with open(f"{output_dir}/batch_embeddings.txt", "w", encoding="utf-8") as f:
            for idx, result in enumerate(batch_results, 1):
                embedding = result.data.get("embedding", [])
                f.write(f"批量项 #{idx} | 向量长度: {len(embedding)} | 前20维: {embedding[:20]}...\n")
        print(f"批量结果已保存：{output_dir}/batch_embeddings.txt")

if __name__ == "__main__":
    # 1. 配置初始化
    api_key = os.environ.get("ARK_API_KEY") or input("输入API密钥: ").strip()
    if not api_key:
        raise ValueError("API密钥不能为空")
    
    # 2. 初始化批量处理器
    embedder = MultimodalEmbedder(api_key=api_key, batch_size=10, retries=2)
    
    # 3. 构造批量输入数据
    batch_inputs = [
        [{"type": "text", "text": "天很蓝，海很深"}, {"type": "image_url", "image_url": {"url": "https://example.com/image1.jpg"}}],
        [{"type": "text", "text": "阳光明媚的沙滩"}, {"type": "image_url", "image_url": {"url": "https://example.com/image2.jpg"}}]
    ]
    
    # 4. 执行批量处理+结果保存
    try:
        batch_results = asyncio.run(embedder.batch_process(batch_inputs))
        embedder.batch_save(batch_results)
        print(f"批量处理完成，共生成 {len(batch_results)} 个向量")
    except Exception as e:
        print(f"批量处理失败: {str(e)}")
```