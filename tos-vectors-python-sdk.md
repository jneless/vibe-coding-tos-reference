# TOS Vectors 概述
TOS Vectors 是对象存储（TOS）为 AI 原生时代设计的存储、查询和管理向量数据的新存储服务


# 相关概念
* 向量桶（Vector Bucket）：向量桶是 TOS Vectors 的顶层命名空间，桶名在 每个区域 每个账户内唯一。在操作 TOS Vectors 上传向量前，需先创建向量桶。为方便理解，你可以将其类比为数据库的库概念；
* 向量索引（Vector Index）：向量索引是 TOS Vectors 的次层命名空间，一个向量桶可以创建多个向量索引，向量索引名字每个向量桶内唯一；一个向量索引相当于向量的容器，用来储存向量数据。在操作 TOS Vectors 上传向量前，需在向量桶下创建向量索引。为方便理解，你可以将其类比为数据库的表概念；
* 向量（Vector）：向量是向量索引中唯一标识某个数据的基本单元，向量由向量名（key）、向量数据（data）、向量元数据（metadata）组成；
* 地域（Region）：地域表示 TOS Vectors 的数据中心所在的地理位置。一般是 cn-beijing、cn-shanghai、cn-guangzhou
* 用户（User）：火山引擎用户UID，一般是 2 开头的10位数字
* 访问域名（Endpoint）：表示 TOS Vectors 对外服务的访问域名。
* 访问密钥 AK（AccessKey ID）：AK
* 私有访问密钥 SK（SecretAccess Key）：SK

# API
## 向量桶操作
创建向量桶：CreateVectorBucket
删除向量桶：DeleteVectorBucket
获取向量桶信息：GetVectorBucket
列出向量桶列表：ListVectorBuckets
设置向量桶策略：PutVectorBucketPolicy
获取向量桶策略：GetVectorBucketPolicy
删除向量桶策略：DeleteVectorBucketPolicy
## 向量索引操作
创建向量索引：CreateIndex
删除向量索引：DeleteIndex
获取向量索引信息：GetIndex
列出向量索引列表：ListIndexes
## 向量操作
上传向量：PutVectors
根据向量 Key 列表获取向量：GetVectors
根据目标向量查询 TopK 相似向量：QueryVectors
删除向量：DeleteVectors
列出向量列表：ListVectors

# 地域和访问域名
* Region Endpoint：用于 Account 下的 CreateVectorBucket/ListVectorBuckets 操作，为固定的域名，参见下表。
* Bucket Endpoint：用于已创建的 Vector Bucket 下的 API 操作，需要拼接 Vector Bucket/Account ID 至域名中，参见下表。

## Region Endpoint
内网：tosvectors-cn-beijing.ivolces.com
外网：tosvectors-cn-beijing.volces.com

## Bucket Endpoint
内网：{VectorBucketName}-{AccountID}.tosvectors-cn-beijing.ivolces.com
外网：{VectorBucketName}-{AccountID}.tosvectors-cn-beijing.volces.com



# 响应码

## 状态码

请求返回的服务端状态码和提示信息如下所示。

| 状态码 | 说明 |
| --- | --- |
| **2XX** | 请求成功，服务端返回用户请求的数据。 |
| **3XX** | 重定向相关请求，客户端需要采取其他操作才能完成请求。 |
| **4XX** | 客户端的请求有错误，服务器没有进行新建或修改数据的操作。 |
| **5XX** | 服务端发生错误，用户将无法判断发出的请求是否成功。 |

## 错误码

当客户端调用接口出错时，将不会返回结果数据。您可以根据每个接口返回的错误码和错误信息来定位相关问题。当调用出错时，HTTP 请求返回一个 **3XX**、**4XX** 或 **5XX** 的 HTTP 状态码。返回的消息体中是具体的错误代码及错误信息。

### 错误响应

如果失败，返回如下 Json 格式的错误 HTTP Body。

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| **Code** | string | 错误响应对应的 HTTP 消息返回码。错误代码是唯一标识错误条件的字符串。 |
| **RequestId** | string | 请求返回的请求 ID，可用于错误定位。 |
| **HostId** | string | 返回该消息的服务端 ID。 |
| **Message** | string | 错误响应消息中具体错误详细的英文描述。 |
| **EC** | string | 细分错误 EC 码。 |


### 错误码信息

| 错误 | HTTP 状态码 | 说明 |
| --- | --- | --- |
| **AccessDeniedException** | 403 | 访问被拒绝。 |
| **ConflictException** | 409 | 请求失败，因为向量存储桶名称或向量索引名称已存在。向量存储桶名称在每个区域的每个账号内必须是唯一的，向量索引名称在向量存储桶内必须是唯一的。 |
| **InternalServerException** | 500 | 由于内部服务器错误，请求失败。 |
| **ServiceQuotaExceededException** | 402 | 您的请求超过了服务配额。 |
| **ServiceUnavailableException** | 503 | 服务不可用。请稍等片刻后重试请求。如果问题持续，请增加重试之间的等待时间。 |
| **TooManyRequestsException** | 429 | 由于请求节流，请求被拒绝。 |
| **ValidationException** | 400 | 请求的操作无效。 |

# GetVectorBucket

## 功能描述

用于查询向量桶的信息。

## 请求消息样式

Endpoint 需使用 Bucket Endpoint。

```http
POST /GetVectorBucket HTTP/1.1
Host: {VectorBucketName}-{AccountID}.tosvectors-cn-beijing.volces.com
Authorization: authorization string
Date: Mon, 10 Dec 2025 12:49:14 GMT

{
    "vectorBucketName": "test-vector-bucket"
}

```

## 请求参数和消息头

无。

## 请求元素

| 名称 | 参数类型 | 是否必选 | 示例值 | 说明 |
| --- | --- | --- | --- | --- |
| vectorBucketName | string | 是 | test-vector-bucket | 要获取的向量桶的名称。 |

## 响应消息头

返回公共的响应头，请参见公共参数。

## 响应元素

如果操作成功，服务将返回一个 HTTP 200 响应以及如下响应元素。
如果操作失败，返回响应码中定义的错误码。

| 名称 | 参数类型 | 示例值 | 说明 |
| --- | --- | --- | --- |
| creationTime | number | 1765792988 | 存储桶的创建时间。 |
| vectorBucketTrn | string | trn:tosvectors:cn-beijing:210676****:bucket/test-vector-bucket | 向量存储桶的 TRN。 |
| vectorBucketName | string | test-vector-bucket | 向量存储桶的名称。 |
| projectName | string | default | 所属项目，当前固定为默认 `default` 。 |


# ListVectorBuckets

## 功能描述

用于列举向量桶列表。

## 请求消息样式

Endpoint 需使用 Region Endpoint。

```http
POST /ListVectorBuckets HTTP/1.1
Host: tosvectors-cn-beijing.volces.com
Authorization: authorization string
Date: Mon, 10 Dec 2025 12:49:14 GMT

{
    "maxResults": 2
}

```

## 请求参数和消息头

无。

## 请求元素

| 名称 | 参数类型 | 是否必选 | 示例值 | 说明 |
| --- | --- | --- | --- | --- |
| maxResults | integer | 否 | 5 | 在响应中返回的向量存储桶的最大数量。不设置默认返回 100 条。取值范围：最小 1，最大 500。 |
| nextToken | string | 否 | dGVzdc12ZWN0b3ltynt0MQ== | 上一个分页令牌。用于获取下一页的结果。长度限制：最小 1，最大 512。 |
| prefix | string | 否 | test | 将响应限制为以指定前缀开头的向量存储桶。长度限制：最小 1，最大 63。 |

## 响应消息头

返回公共的响应头，请参见公共参数。

## 响应元素

如果操作成功，服务将返回一个 HTTP 200 响应以及如下响应元素。
如果操作失败，返回响应码中定义的错误码。

| 名称 | 参数类型 | 示例值 | 说明 |
| --- | --- | --- | --- |
| nextToken | string | dGVzdc12ZWN0b3ltynt0MQ== | 当有更多存储桶需要通过分页列出时，该元素会包含在响应中。这是用于获取下一页结果的令牌。长度限制：最小 1，最大 512。 |
| vectorBuckets | VectorBucketSummary[] | 见下文结构体定义 | 请求者拥有的向量存储桶列表。 |

VectorBucketSummary 定义如下：

| 名称 | 参数类型 | 示例值 | 说明 |
| --- | --- | --- | --- |
| creationTime | number | 1765792988 | 存储桶的创建时间。 |
| vectorBucketTrn | string | trn:tosvectors:cn-beijing:210676****:bucket/test-vector-bucket | 向量存储桶的 TRN。 |
| vectorBucketName | string | test-vector-bucket | 向量存储桶的名称。 |
| projectName | string | default | 所属项目。 |

# DeleteVectorBucket

## 功能描述

删除已经创建的向量桶，删除向量桶之前，要保证向量桶是空桶，即桶中的向量索引已经被删除掉。

## 请求消息样式

Endpoint 需使用 Bucket Endpoint。

```http
POST /DeleteVectorBucket HTTP/1.1
Host: {VectorBucketName}-{AccountID}.tosvectors-cn-beijing.volces.com
Authorization: authorization string
Date: Mon, 10 Dec 2025 12:49:14 GMT

{
    "vectorBucketName": "test-vector-bucket"
}

```

## 请求参数和消息头

无。

## 请求元素

| 名称 | 参数类型 | 是否必选 | 示例值 | 说明 |
| --- | --- | --- | --- | --- |
| vectorBucketName | string | 是 | test-vector-bucket | 要删除的向量桶的名称。 |

## 响应消息头

返回公共的响应头，请参见公共参数。

## 响应元素

如果操作成功，服务将返回一个 HTTP 200 响应。
如果操作失败，返回响应码中定义的错误码。

# CreateVectorBucket

## 功能描述

创建一个新的向量桶。要创建向量桶，您必须注册火山引擎账号，并拥有一个有效的 TOS Vectors AccessKey ID 来验证请求。不允许匿名请求创建桶，并且一个用户在全局内最多可创建 100 个向量桶（包含普通桶）。通过创建向量桶，您就成为向量桶的所有者。

## 请求消息样式

Endpoint 需使用 Region Endpoint。

```http
POST /CreateVectorBucket HTTP/1.1
Host: tosvectors-cn-beijing.volces.com
Authorization: authorization string
Date: Mon, 10 Dec 2025 12:49:14 GMT

{
    "vectorBucketName": "test-vector-bucket"
}

```

## 请求参数和消息头

无。

## 请求元素

| 名称 | 参数类型 | 是否必选 | 示例值 | 说明 |
| --- | --- | --- | --- | --- |
| vectorBucketName | string | 是 | test-vector-bucket | 要创建的向量桶的名称约束：<ul><li>最小长度为 3，最大长度为 32。</li><li>只能包含小写英文字母 (a - z)、数字 (0 - 9) 以及短横线 (-)，不支持大写字母、下划线、中文或其他特殊字符。</li><li>桶名的开头和结尾只能是小写字母或数字，不能以短横线开头或结尾。</li><li>单账号单 Region 下名称必须唯一。</li></ul> |

## 响应消息头

返回公共的响应头，请参见公共参数。

## 响应元素

如果操作成功，服务将返回一个 HTTP 200 响应。
如果操作失败，返回响应码中定义的错误码。

# CreateIndex

## 功能描述

在向量桶下创建向量索引，向量索引名需要在向量桶中唯一。

## 请求消息样式

Endpoint 需使用 Bucket Endpoint。

```http
POST /CreateIndex HTTP/1.1
Host: {VectorBucketName}-{AccountID}.tosvectors-cn-beijing.volces.com
Authorization: authorization string
Date: Mon, 10 Dec 2025 12:49:14 GMT

{
    "vectorBucketName": "test-vector-bucket",
    "indexName": "test-index",
    "dataType": "float32",
    "dimension": 5,
    "distanceMetric": "euclidean",
    "metadataConfiguration": {
        "nonFilterableMetadataKeys": [
            "key_c",
            "key_d"
        ]
    }
}

```

## 请求参数和消息头

无。

## 请求元素

| 名称 | 参数类型 | 是否必选 | 示例值 | 说明 |
| --- | --- | --- | --- | --- |
| vectorBucketName | string | 是 | test-vector-bucket | 要在其中创建向量索引的向量存储桶的名称。 |
| indexName | string | 是 | test-index | 要创建的向量索引的名称。长度限制：最小长度为 3，最大长度为 63。 |
| dataType | string | 是 | float32 | 要插入到向量索引中的向量的数据类型。有效值：`float32`。 |
| dimension | integer | 是 | 5 | 要插入到向量索引中的向量的维度。有效范围：最小值为 1，最大值为 4096。 |
| distanceMetric | string | 是 | euclidean | 用于相似性搜索的距离度量。有效值：`euclidean |
| metadataConfiguration | MetadataConfiguration | 否 | 见示例值 | 向量索引的元数据配置。 |

MetadataConfiguration 定义如下：

| 参数名称 | 参数类型 | 示例值 | 说明 |
| --- | --- | --- | --- |
| nonFilterableMetadataKeys | string[] | ["key_c", "key_d"] | 不可用于筛选的元数据键列表。 |

## 响应消息头

返回公共的响应头，请参见公共参数。

## 响应元素

如果操作成功，服务将返回一个 HTTP 200 响应。
如果操作失败，返回响应码中定义的错误码。

# DeleteIndex

## 功能描述

删除向量桶的向量索引。

## 请求消息样式

Endpoint 需使用 Bucket Endpoint。

```http
POST /DeleteIndex HTTP/1.1
Host: {VectorBucketName}-{AccountID}.tosvectors-cn-beijing.volces.com
Authorization: authorization string
Date: Mon, 10 Dec 2025 12:49:14 GMT

{
    "vectorBucketName": "test-vector-bucket",
    "indexName": "test-index"
}

```

## 请求参数和消息头

无。

## 请求元素

| 名称 | 参数类型 | 是否必选 | 示例值 | 说明 |
| --- | --- | --- | --- | --- |
| vectorBucketName | string | 是 | test-vector-bucket | 要删除的向量桶的名称。 |
| indexName | string | 是 | test-index | 要删除的向量索引的名称。 |

## 响应消息头

返回公共的响应头，请参见公共参数。

## 响应元素

如果操作成功，服务将返回一个 HTTP 200 响应。
如果操作失败，返回响应码中定义的错误码。

# GetIndex

## 功能描述

获取向量桶的向量索引信息。

## 请求消息样式

Endpoint 需使用 Bucket Endpoint。

```http
POST /GetIndex HTTP/1.1
Host: {VectorBucketName}-{AccountID}.tosvectors-cn-beijing.volces.com
Authorization: authorization string
Date: Mon, 10 Dec 2025 12:49:14 GMT

{
    "vectorBucketName": "test-vector-bucket",
    "indexName": "test-index"
}

```

## 请求参数和消息头

无。

## 请求元素

| 名称 | 参数类型 | 是否必选 | 示例值 | 说明 |
| --- | --- | --- | --- | --- |
| vectorBucketName | string | 是 | test-vector-bucket | 要获取的向量索引所属向量桶的名称。 |
| indexName | string | 是 | test-index | 要获取的向量索引的名称。 |

## 响应消息头

返回公共的响应头，请参见公共参数。

## 响应元素

如果操作成功，服务将返回一个 HTTP 200 响应以及如下响应元素。
如果操作失败，返回响应码中定义的错误码。

| 名称 | 参数类型 | 示例值 | 说明 |
| --- | --- | --- | --- |
| index | Index | 参见 Index 定义 | 索引创建的时间戳。 |

Index 结构体参数说明：

| 名称 | 参数类型 | 示例值 | 说明 |
| --- | --- | --- | --- |
| vectorBucketName | string | test-vector-bucket | 向量存储桶的名称。 |
| indexName | string | test-index | 向量索引的名称。 |
| creationTime | number | 1764319996 | 索引创建的时间戳。 |
| dataType | string | float32 | 向量的数据类型。 |
| dimension | number | 5 | 向量的维度。 |
| distanceMetric | string | euclidean | 用于相似性搜索的距离度量。 |
| indexTrn | string | trn:tosvectors:cn-beijing:11111:bucket/test-vector-bucket/index/test-index | 向量索引的 TRN。 |
| metadataConfiguration | MetadataConfiguration | { "nonFilterableMetadataKeys": ["key_c", "key_d"] } | 元数据配置。 |

MetadataConfiguration 结构体参数说明：

| 名称 | 参数类型 | 示例值 | 说明 |
| --- | --- | --- | --- |
| nonFilterableMetadataKeys | string[] | ["key_c", "key_d"] | 不可用于筛选的元数据键列表。 |

# ListIndexes

## 功能描述

获取向量桶的向量索引信息。

## 请求消息样式

Endpoint 需使用 Bucket Endpoint。

```http
POST /ListIndexes HTTP/1.1
Host: {VectorBucketName}-{AccountID}.tosvectors-cn-beijing.volces.com
Authorization: authorization string
Date: Mon, 10 Dec 2025 12:49:14 GMT

{
    "vectorBucketName": "test-vector-bucket",
    "maxResults": 100,
    "nextToken": ""
}

```

## 请求参数和消息头

无。

## 请求元素

| 名称 | 参数类型 | 是否必选 | 示例值 | 说明 |
| --- | --- | --- | --- | --- |
| vectorBucketName | string | 是 | test-vector-bucket | 包含向量索引的向量存储桶的名称。 |
| maxResults | integer | 否 | 100 | 响应中返回的最大项目数。不设置默认返回 100 条。有效范围：最小值为 1，最大值为 500。 |
| nextToken | string | 否 | 1b802ff5test-index-3 | 上一个分页令牌。用于获取下一页结果。长度限制：最小长度为 1，最大长度为 512。 |
| prefix | string | 否 | test | 将响应限制为以指定前缀开头的向量索引。长度限制：最小长度为 1，最大长度为 63。 |

## 响应消息头

返回公共的响应头，请参见公共参数。

## 响应元素

如果操作成功，服务将返回一个 HTTP 200 响应以及如下响应元素。
如果操作失败，返回响应码中定义的错误码。

| 名称 | 参数类型 | 示例值 | 说明 |
| --- | --- | --- | --- |
| indexes | IndexSummary[] | 参见下表 | 向量索引列表。 |
| nextToken | string | 1b802ff5test-index-3 | 下一个分页令牌。如果响应中包含此令牌，表示还有更多结果。可以在下一个请求中使用此令牌来获取下一页结果。长度限制：最小长度为 1，最大长度为 512。 |

IndexSummary 结构体参数说明：

| 参数名称 | 参数类型 | 示例值 | 说明 |
| --- | --- | --- | --- |
| vectorBucketName | string | test-vector-bucket | 向量向量存储桶名称。 |
| indexName | string | test-index | 向量索引名称。 |
| creationTime | number | 1764580825 | 向量索引创建时间。 |
| indexTrn | string | trn:tosvectors:cn-beijing:11111:bucket/test-vector-bucket/index/test-index | 向量索引的 TRN。 |

# GetVectorBucketPolicy

## 功能描述

用于查询向量桶的 Policy。

## 请求消息样式

Endpoint 需使用 Bucket Endpoint。

```http
POST /GetVectorBucketPolicy HTTP/1.1
Host: {VectorBucketName}-{AccountID}.tosvectors-cn-beijing.volces.com
Authorization: authorization string
Date: Mon, 10 Dec 2025 12:49:14 GMT

{
    "vectorBucketName": "test-vector-bucket"
}

```

## 请求参数和消息头

无。

## 请求元素

| 名称 | 参数类型 | 是否必选 | 示例值 | 说明 |
| --- | --- | --- | --- | --- |
| vectorBucketName | string | 是 | test-vector-bucket | 要获取 Policy 的向量桶的名称。 |

## 响应消息头

返回公共的响应头，请参见公共参数。

## 响应元素

如果操作成功，服务将返回一个 HTTP 200 响应以及如下响应元素。
如果操作失败，返回响应码中定义的错误码。

| 名称 | 参数类型 | 示例值 | 说明 |
| --- | --- | --- | --- |
| policy | string | 见示例 | 定义策略的 JSON 字符串。 |

```
'{
"Version": "2025-12-01",
"Statement": [{
"Effect": "Allow",
"Principal": [
"210004xxxx/testuser"
],
"Action": [
"tosvectors:*"
],
"Resource": [
"trn:tosvectors:cn-beijing:11111:bucket/test-vector-bucket"
]
}]
}'
```


# DeleteVectorBucketPolicy

## 功能描述

删除向量桶的 IAM Policy。

## 请求消息样式

Endpoint 需使用 Bucket Endpoint。

```http
POST /DeleteVectorBucketPolicy HTTP/1.1
Host: {VectorBucketName}-{AccountID}.tosvectors-cn-beijing.volces.com
Authorization: authorization string
Date: Mon, 10 Dec 2025 12:49:14 GMT

{
    "vectorBucketName": "test-vector-bucket"
}

```

## 请求参数和消息头

无。

## 请求元素

| 名称 | 参数类型 | 是否必选 | 示例值 | 说明 |
| --- | --- | --- | --- | --- |
| vectorBucketName | string | 是 | test-vector-bucket | 要删除的向量桶的名称。 |

## 响应消息头

返回公共的响应头，请参见公共参数。

## 响应元素

如果操作成功，服务将返回一个 HTTP 200 响应。
如果操作失败，返回响应码中定义的错误码。

# PutVectorBucketPolicy

## 功能描述

设置向量桶的 IAM Policy。

## 请求消息样式

Endpoint 需使用 Bucket Endpoint。

```http
POST /PutVectorBucketPolicy HTTP/1.1
Host: {VectorBucketName}-{AccountID}.tosvectors-cn-beijing.volces.com
Authorization: authorization string
Date: Mon, 10 Dec 2025 12:49:14 GMT

{
    "policy": "{\"Version\": \"2025-12-01\", \"Statement\": [{\"Effect\":\"Allow\",\"Principal\":[\"*\"],\"Action\":[\"tosvectors:ListVectorBuckets\"],\"Resource\":[\"trn:tosvectors:cn-beijing:2106764132:bucket/test-vector-bucket\"]}]}",
    "vectorBucketName": "test-vector-bucket"
}

```

## 请求参数和消息头

无。

## 请求元素

| 名称 | 参数类型 | 是否必选 | 示例值 | 说明 |
| --- | --- | --- | --- | --- |
| policy | string | 是 | 见示例 | 定义策略的 JSON 文本 |
| vectorBucketName | string | 是 | test-vector-bucket | 向量存储桶的名称。长度限制：最小 3，最大 32。vectorBucketName 必须提供。|

* TRN 定义：
    * 向量桶：`trn:tosvectors:<region>:<account-id>:bucket/<bucket-name>`
    * 向量索引：`trn:tosvectors:<region>:<account-id>:bucket/<bucket-name>/index/<index-name>` 



## 响应消息头

返回公共的响应头，请参见公共参数。

## 响应元素

如果操作成功，服务将返回一个 HTTP 200 响应。
如果操作失败，返回响应码中定义的错误码。

# GetVectors

## 功能描述

从向量索引中根据向量 key 批量查询向量。

## 请求消息样式

Endpoint 需使用 Bucket Endpoint。

```http
POST /GetVectors HTTP/1.1
Host: {VectorBucketName}-{AccountID}.tosvectors-cn-beijing.volces.com
Authorization: authorization string
Date: Mon, 10 Dec 2025 12:49:14 GMT

{
    "vectorBucketName": "test-vector-bucket",
    "indexName": "test-index",
    "keys": ["key0", "key1"],
    "returnData": true,
    "returnMetadata": true
}

```

## 请求参数和消息头

无。

## 请求元素

| 名称 | 参数类型 | 是否必选 | 示例值 | 说明 |
| --- | --- | --- | --- | --- |
| vectorBucketName | string | 是 | test-vector-bucket | 向量桶的名称。 |
| indexName | string | 是 | test-index | 向量索引的名称。 |
| keys | string[] | 是 | ["key0", "key1"] | 您想要返回其属性的向量名称列表。数组项数：最少 1 项，最多 100 项。字符串长度：最少 1 个字符，最多 1024 个字符。 |
| returnData | boolean | 否 | true | 指示是否在响应中包含向量数据。默认值：`false`。 |
| returnMetadata | boolean | 否 | true | 指示是否在响应中包含元数据。默认值：`false`。 |

## 响应消息头

返回公共的响应头，请参见公共参数。

## 响应元素

如果操作成功，服务将返回一个 HTTP 200 响应以及如下响应元素。
如果操作失败，返回响应码中定义的错误码。

| 名称 | 参数类型 | 示例值 | 说明 |
| --- | --- | --- | --- |
| vectors | GetOutputVector[] | 参见下表 | 包含向量属性的数组。 |

GetOutputVector 结构体参数说明：

| 名称 | 参数类型 | 示例值 | 说明 |
| --- | --- | --- | --- |
| data | VectorData | "float32": [0.1, 0.1, 0.1, 0.1, 0.1] | 向量数据。确保查询向量的维度与被查询的索引维度相同。 |
| key | string | key0 | 向量的唯一标识符。 |
| metadata | JSON value | { "key_a": "va", "key_b": "vb", "key_c": "vc" } | 与向量关联的元数据。 |

VectorData 结构体是 Union 类型，当前只有一个成员有效（float32），参见下表：

| 名称 | 参数类型 | 说明 |
| --- | --- | --- |
| float32 | float32[] | 向量数据。该向量维度必须和 CreateIndex 中定义的向量索引维度一致。 |

# DeleteVectors

## 功能描述

从向量索引中根据向量 key 批量删除向量。

## 请求消息样式

Endpoint 需使用 Bucket Endpoint。

```http
POST /DeleteVectors HTTP/1.1
Host: {VectorBucketName}-{AccountID}.tosvectors-cn-beijing.volces.com
Authorization: authorization string
Date: Mon, 10 Dec 2025 12:49:14 GMT

{
    "vectorBucketName": "test-vector-bucket",
    "indexName": "test-index",
    "keys": ["key0", "key1"]
}

```

## 请求参数和消息头

无。

## 请求元素

| 名称 | 参数类型 | 是否必选 | 示例值 | 说明 |
| --- | --- | --- | --- | --- |
| vectorBucketName | string | 是 | test-vector-bucket | 向量桶的名称。 |
| indexName | string | 是 | test-index | 向量索引的名称。 |
| keys | string[] | 是 | ["key0", "key1"] | 您想要返回其属性的向量名称列表。数组项数：最少 1 项，最多 100 项。 字符串长度：最少 1 个字符，最多 1024 个字符。 |

## 响应消息头

返回公共的响应头，请参见公共参数。

## 响应元素

如果操作成功，服务将返回一个 HTTP 200 响应。
如果操作失败，返回响应码中定义的错误码。

# ListVectors

## 功能描述

从向量索引中分页查询向量。

## 请求消息样式

Endpoint 需使用 Bucket Endpoint。

```http
POST /ListVectors HTTP/1.1
Host: {VectorBucketName}-{AccountID}.tosvectors-cn-beijing.volces.com
Authorization: authorization string
Date: Mon, 10 Dec 2025 12:49:14 GMT

{
    "vectorBucketName": "test-vector-bucket",
    "indexName": "test-index",
    "returnData": true,
    "returnMetadata": true,
    "maxResults": 100
}

```

## 请求参数和消息头

无。

## 请求元素

| 名称 | 参数类型 | 是否必选 | 示例值 | 说明 |
| --- | --- | --- | --- | --- |
| vectorBucketName | string | 是 | test-vector-bucket | 向量桶的名称。 |
| indexName | string | 是 | test-index | 向量索引的名称。 |
| maxResults | integer | 否 | 100 | 页面上返回的最大向量数。默认值为 500。如果处理的数据集大小在这值之前超过 1MB，操作将停止并返回已检索的向量以及用于后续请求的令牌。取值范围：1 到 1000。 |
| nextToken | string | 否 | a2V5MA== | 来自前一个请求的分页令牌。对于初始请求，此字段的值为空。长度约束：最小长度为 1，最大长度为 2048。 |
| returnData | boolean | 否 | true | 如果为 `true`，则每个向量的向量数据将包含在响应中。默认值：`false`。 |
| returnMetadata | boolean | 否 | true | 如果为 `true`，则与每个向量关联的元数据将包含在响应中。默认值：`false`。 |

## 响应元素

如果操作成功，服务将返回一个 HTTP 200 响应以及如下响应元素。
如果操作失败，返回响应码中定义的错误码。

| 字段 | 类型 | 示例值 | 说明 |
| --- | --- | --- | --- |
| nextToken | string | a2V5MA== | 用于后续请求的分页令牌。如果不需要进一步分页，则为空。 |
| vectors | ListOutputVector[] | 参见下表 | 当前段中的向量。数组中的每个对象是一个 ListOutputVector 对象。 |

ListOutputVector 结构体参数说明：

| 参数名称 | 参数类型 | 示例值 | 说明 |
| --- | --- | --- | --- |
| data | VectorData | { "float32": [0.1, 0.1, 0.1, 0.1, 0.1] } | 向量数据。确保查询向量的维度与被查询的索引维度相同。 |
| key | string | key0 | 向量的唯一标识符。 |
| metadata | JSON value | { "key_a": "va", "key_b": "vb", "key_c": "vc" } | 与向量关联的元数据。 |

VectorData 结构体是 Union 类型，当前只有一个成员有效（float32），参见下表：

| 名称 | 参数类型 | 示例值 | 说明 |
| --- | --- | --- | --- |
| float32 | float32[] | [0.1, 0.1, 0.1, 0.1, 0.1] | 向量数据。该向量维度必须和 CreateIndex 中定义的向量索引维度一致。 |

# PutVectors

## 功能描述

往向量索引中批量插入向量。

## 请求消息样式

Endpoint 需使用 Bucket Endpoint。

```http
POST /PutVectors HTTP/1.1
Host: {VectorBucketName}-{AccountID}.tosvectors-cn-beijing.volces.com
Authorization: authorization string
Date: Mon, 10 Dec 2025 12:49:14 GMT

{
    "vectorBucketName": "test-vector-bucket",
    "indexName":"test-index",
    "vectors": [
        {
            "key": "key0",
            "metadata": {
                "key_a": "va",
                "key_b": "vb",
                "key_c": "vc"
            },
            "data":{
                "float32": [0.1, 0.1, 0.1, 0.1, 0.1]
            }
        },
        {
            "key": "key1",
            "metadata": {
                "key_a": "va",
                "key_b": "vb",
                "key_c": "vc1",
                "key_d": "vd"
            },
            "data":{
                "float32": [0.2, 0.2, 0.2, 0.2, 0.2]
            }
        }
    ]
}

```

## 请求参数和消息头

无。

## 请求元素

| 名称 | 参数类型 | 是否必选 | 示例值 | 说明 |
| --- | --- | --- | --- | --- |
| vectorBucketName | string | 是 | test-vector-bucket | 向量桶名称。 |
| indexName | string | 是 | test-index | 向量索引名称。 |
| vectors | PutInputVector[] | 是 | 参见下表 | 要添加到向量索引的向量数组。单个请求中的向量数量不能超过资源容量（最少 1 个，最多 500 个），否则请求将被拒绝并返回 `ServiceUnavailableException` 错误。 |

PutInputVector 结构体参数说明：

| 名称 | 参数类型 | 示例值 | 说明 |
| --- | --- | --- | --- |
| data | VectorData | "float32": [0.1, 0.1, 0.1, 0.1, 0.1] | 向量数据。确保查询向量的维度与被查询的向量索引的维度相同。 |
| key | string | key0 | 向量的唯一标识符。 |
| metadata | JSON value | { "key_a": "va", "key_b": "vb", "key_c": "vc" } | 与向量关联的元数据，用户自定义 KV 组成的 Map。 |

VectorData 结构体是 Union 类型，当前只有一个成员有效 (float32)，参见下表：

| 名称 | 参数类型 | 说明 |
| --- | --- | --- |
| float32 | float32[] | 向量数据。该向量维度必须和 CreateIndex 中定义的向量索引维度一致。 |

## 响应消息头

返回公共的响应头，请参见公共参数。

## 响应元素

如果操作成功，服务将返回一个 HTTP 200 响应。
如果操作失败，返回响应码中定义的错误码。

# QueryVectors

## 功能描述

从向量索引中根据一个目标向量 KNN 查询距离最近 TopK 向量。

## 请求消息样式

Endpoint 需使用 Bucket Endpoint。

```http
POST /QueryVectors HTTP/1.1
Host: {VectorBucketName}-{AccountID}.tosvectors-cn-beijing.volces.com
Authorization: authorization string
Date: Mon, 10 Dec 2025 12:49:14 GMT

{
    "vectorBucketName": "test-vector-bucket",
    "indexName":"test-index",
    "queryVector": {
        "float32": [
            0.1,
            0.1,
            0.1,
            0.1,
            0.1
        ]
    },
    "topK": 10,
    "returnDistance": true,
    "returnMetadata": true,
    "filter": {"$and": [{"key_a": "va"}]}
}

```

## 请求参数和消息头

无。

## 请求元素

| 名称 | 参数类型 | 是否必选 | 示例值 | 说明 |
| --- | --- | --- | --- | --- |
| vectorBucketName | string | 是 | test-vector-bucket | 向量桶的名称。 |
| indexName | string | 是 | test-index | 向量索引的名称。 |
| filter | JSON value | 否 | {"$and": [{"key_a": "va"}]} | 在查询期间应用的元数据筛选器。 |
| queryVector | VectorData | 见示例值 | { "float32": [0.1, 0.1, 0.1, 0.1, 0.1] } | 查询向量。确保查询向量的维度与被查询的索引维度相同。 |
| returnDistance | boolean | 否 | true | 指示是否在响应中包含计算出的距离。默认值：`false`。 |
| returnMetadata | boolean | 否 | true | 指示是否在响应中包含元数据。默认值：`false`。 |
| topK | integer | 是 | 10 | 为每个查询返回的结果数量。有效范围：最小值为 1，最大值为 1000。 |

VectorData 结构体是 Union 类型，当前只有一个成员有效（float32）：

| 名称 | 参数类型 | 示例值 | 说明 |
| --- | --- | --- | --- |
| float32 | float32[] | [0.1, 0.1, 0.1, 0.1, 0.1] | 向量数据。该向量维度必须和 CreateIndex 中定义的向量索引维度一致。 |

## 响应元素

如果操作成功，服务将返回一个 HTTP 200 响应以及如下响应元素。
如果操作失败，返回响应码中定义的错误码。

| 字段 | 类型 | 示例值 | 说明 |
| --- | --- | --- | --- |
| vectors | []QueryOutputVector | 参见下表 | 包含向量搜索结果的数组。 |

QueryOutputVector 结构体参数说明：

| 参数名称 | 参数类型 | 示例值 | 说明 |
| --- | --- | --- | --- |
| data | VectorData | [0.1, 0.1, 0.1, 0.1, 0.1] | 向量数据。确保查询向量的维度与被查询的索引维度相同。 |
| distance | float | 0.050000004 | 查询向量与结果向量之间的距离。 |
| key | string | key0 | 向量的唯一标识符。 |
| metadata | JSON value | { "key_a": "va", "key_b": "vb", "key_c": "vc1", "key_d": "vd" } | 与向量关联的元数据。 |


# SDK

## CreateVectorBucket

```
# 创建向量存储桶
import os
import tos

# 从环境变量获取 AK 和 SK 信息。
ak = os.getenv('TOS_ACCESS_KEY')
sk = os.getenv('TOS_SECRET_KEY')
# your endpoint 和 your region 填写Bucket 所在区域对应的Endpoint。# 以华北2(北京)为例，your endpoint 填写 https://tosvectors-cn-beijing.volces.com，your region 填写 cn-beijing。
endpoint = 'Provide your endpoint'
region = 'Provide your region'
vector_bucket_name = 'Provide your bucket name'

try:
    # 创建 VectorClient 对象，对向量的操作都通过 VectorClient 实现
    client = tos.VectorClient(ak, sk, endpoint, region)
    
    # 创建向量存储桶，向量存储桶名称需要满足以下规则：
    # 1. 长度为3-32个字符
    # 2. 只能包含小写字母、数字和连字符(-)
    # 3. 不能以连字符(-)开头或结尾
    result = client.create_vector_bucket(vector_bucket_name)
    
    # 打印创建成功的信息
    print('向量存储桶创建成功')
    print('Request ID: {}'.format(result.request_id))
    print('状态码: {}'.format(result.status_code))
    
except tos.exceptions.TosClientError as e:
    # 操作失败，捕获客户端异常，一般情况为非法请求参数或网络异常
    print('fail with client error, message:{}, cause: {}'.format(e.message, e.cause))
except tos.exceptions.TosServerError as e:
    # 操作失败，捕获服务端异常，可从返回信息中获取详细错误信息
    print('fail with server error, code: {}'.format(e.code))
    # request id 可定位具体问题，强烈建议日志中保存
    print('error with request id: {}'.format(e.request_id))
    print('error with message: {}'.format(e.message))
    print('error with http code: {}'.format(e.status_code))
    print('error with ec: {}'.format(e.ec))
    print('error with request url: {}'.format(e.request_url))
except Exception as e:
    print('fail with unknown error: {}'.format(e))
```

## DeleteVectorBucket
```
# 删除向量存储桶基本用法
import os
import tos

# 从环境变量获取 AK 和 SK 信息。
ak = os.getenv('TOS_ACCESS_KEY')
sk = os.getenv('TOS_SECRET_KEY')
# your endpoint 和 your region 填写Bucket 所在区域对应的Endpoint。# 以华北2(北京)为例，your endpoint 填写 https://tosvectors-cn-beijing.volces.com，your region 填写 cn-beijing。
endpoint = 'Provide your endpoint'
region = 'Provide your region'
vector_bucket_name = 'Provide your bucket name'

# 账户ID，用于向量存储桶操作
account_id = os.getenv('TOS_ACCOUNT_ID')

try:
    # 创建 VectorClient 对象，对向量的操作都通过 VectorClient 实现
    client = tos.VectorClient(ak, sk, endpoint, region)
    
    # 删除向量存储桶
    # 参数说明：
    # - vector_bucket_name: 要删除的向量存储桶名称
    # - account_id: 账户ID，用于权限验证
    result = client.delete_vector_bucket(vector_bucket_name, account_id)
    
    # 打印删除结果
    print('删除向量存储桶成功')
    print('Request ID:', result.request_id)
    print('Status Code:', result.status_code)
    
except tos.exceptions.TosClientError as e:
    # 操作失败，捕获客户端异常，一般情况为非法请求参数或网络异常
    print('fail with client error, message:{}, cause: {}'.format(e.message, e.cause))
except tos.exceptions.TosServerError as e:
    # 操作失败，捕获服务端异常，可从返回信息中获取详细错误信息
    print('fail with server error, code: {}'.format(e.code))
    # request id 可定位具体问题，强烈建议日志中保存
    print('error with request id: {}'.format(e.request_id))
    print('error with message: {}'.format(e.message))
    print('error with http code: {}'.format(e.status_code))
    print('error with ec: {}'.format(e.ec))
    print('error with request url: {}'.format(e.request_url))
except Exception as e:
    print('fail with unknown error: {}'.format(e))
```

## GetVectorBucket
```
# 查询指定向量桶的信息
import os
import tos

# 从环境变量获取 AK 和 SK 信息。
ak = os.getenv('TOS_ACCESS_KEY')
sk = os.getenv('TOS_SECRET_KEY')
# your endpoint 和 your region 填写Bucket 所在区域对应的Endpoint。# 以华北2(北京)为例，your endpoint 填写 https://tosvectors-cn-beijing.volces.com，your region 填写 cn-beijing。
endpoint = 'Provide your endpoint'
region = 'Provide your region'
vector_bucket_name = 'Provide your bucket name'

# 从环境变量获取账户ID
account_id = os.getenv('TOS_ACCOUNT_ID')

try:
    # 创建 VectorClient 对象，对向量的操作都通过 VectorClient 实现
    client = tos.VectorClient(ak, sk, endpoint, region)
    
    # 查询指定向量存储桶的详细信息
    # 需要提供向量存储桶名称和账户ID
    result = client.get_vector_bucket(vector_bucket_name, account_id)
    
    # 打印查询成功的信息
    print('向量存储桶信息查询成功')
    print('Request ID: {}'.format(result.request_id))
    print('状态码: {}'.format(result.status_code))
    
    # 打印向量存储桶的详细信息
    if result.vector_bucket:
        print('\n向量存储桶详细信息:')
        print('向量存储桶名称: {}'.format(result.vector_bucket.vector_bucket_name))
        print('向量存储桶TRN: {}'.format(result.vector_bucket.vector_bucket_trn))
        print('创建时间: {}'.format(result.vector_bucket.creation_time))
    
except tos.exceptions.TosClientError as e:
    # 操作失败，捕获客户端异常，一般情况为非法请求参数或网络异常
    print('fail with client error, message:{}, cause: {}'.format(e.message, e.cause))
except tos.exceptions.TosServerError as e:
    # 操作失败，捕获服务端异常，可从返回信息中获取详细错误信息
    print('fail with server error, code: {}'.format(e.code))
    # request id 可定位具体问题，强烈建议日志中保存
    print('error with request id: {}'.format(e.request_id))
    print('error with message: {}'.format(e.message))
    print('error with http code: {}'.format(e.status_code))
    print('error with ec: {}'.format(e.ec))
    print('error with request url: {}'.format(e.request_url))
except Exception as e:
    print('fail with unknown error: {}'.format(e))
```

## ListVectorBuckets

```
# 分页列举向量存储桶
import os
import tos

# 从环境变量获取 AK 和 SK 信息。
ak = os.getenv('TOS_ACCESS_KEY')
sk = os.getenv('TOS_SECRET_KEY')
# your endpoint 和 your region 填写Bucket 所在区域对应的Endpoint。# 以华北2(北京)为例，your endpoint 填写 https://tosvectors-cn-beijing.volces.com，your region 填写 cn-beijing。
endpoint = 'Provide your endpoint'
region = 'Provide your region'
vector_bucket_name = 'Provide your bucket name'

try:
    # 创建 VectorClient 对象，对向量的操作都通过 VectorClient 实现
    client = tos.VectorClient(ak, sk, endpoint, region)
    
    # 第一页：限制返回数量为 2 个存储桶
    page1 = client.list_vector_buckets(max_results=2)
    
    print("=== 第一页结果 ===")
    print(f"Request ID: {page1.request_id}")
    print(f"Status Code: {page1.status_code}")
    print(f"Max Results: 2")
    
    if page1.vector_buckets:
        print(f"Found {len(page1.vector_buckets)} vector buckets in first page:")
        for i, bucket in enumerate(page1.vector_buckets):
            print(f"  {i+1}. Bucket Name: {bucket.vector_bucket_name}")
            print(f"     Bucket TRN: {bucket.vector_bucket_trn}")
    else:
        print("No vector buckets found in first page.")
    
    # 如果有下一页，继续获取第二页
    if page1.next_token:
        print(f"\nNext Token: {page1.next_token}")
        print("\n=== 第二页结果 ===")
        
        # 使用 next_token 获取下一页结果
        page2 = client.list_vector_buckets(max_results=2, next_token=page1.next_token)
        
        print(f"Request ID: {page2.request_id}")
        print(f"Status Code: {page2.status_code}")
        
        if page2.vector_buckets:
            print(f"Found {len(page2.vector_buckets)} vector buckets in second page:")
            for i, bucket in enumerate(page2.vector_buckets):
                print(f"  {i+1}. Bucket Name: {bucket.vector_bucket_name}")
                print(f"     Bucket TRN: {bucket.vector_bucket_trn}")
        else:
            print("No vector buckets found in second page.")
            
        if page2.next_token:
            print(f"\nNext Token: {page2.next_token}")
    else:
        print("\n所有结果已在一页中显示完毕")
        
except tos.exceptions.TosClientError as e:
    # 操作失败，捕获客户端异常，一般情况为非法请求参数或网络异常
    print('fail with client error, message:{}, cause: {}'.format(e.message, e.cause))
except tos.exceptions.TosServerError as e:
    # 操作失败，捕获服务端异常，可从返回信息中获取详细错误信息
    print('fail with server error, code: {}'.format(e.code))
    # request id 可定位具体问题，强烈建议日志中保存
    print('error with request id: {}'.format(e.request_id))
    print('error with message: {}'.format(e.message))
    print('error with http code: {}'.format(e.status_code))
    print('error with ec: {}'.format(e.ec))
    print('error with request url: {}'.format(e.request_url))
except Exception as e:
    print('fail with unknown error: {}'.format(e))
```

## PutVectorBucketPolicy

```
# 设置向量桶的 IAM Policy
import os
import json
import tos

# 从环境变量获取 AK 和 SK 信息。
ak = os.getenv("TOS_ACCESS_KEY")
sk = os.getenv("TOS_SECRET_KEY")
# your endpoint 和 your region 填写Bucket 所在区域对应的Endpoint。# 以华北2(北京)为例，your endpoint 填写 https://tosvectors-cn-beijing.volces.com，your region 填写 cn-beijing。
endpoint = os.getenv("TOS_ENDPOINT")
region = os.getenv("TOS_REGION")
vector_bucket_name = os.getenv("TOS_BUCKET")
account_id = os.getenv("TOS_ACCOUNT_ID")

try:
    # 创建 VectorClient 对象，对向量的操作都通过 VectorClient 实现
    client = tos.VectorClient(ak, sk, endpoint, region)

    # 获取账户ID

    # 定义IAM策略，允许对向量桶的访问权限
    policy = {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Effect": "Allow",
                "Principal": [account_id],
                "Action": "tosvectors:GetVectorBucket",
                "Resource": f"trn:tosvectors:{region}:{account_id}:bucket/{vector_bucket_name}",
            }
        ],
    }

    # 将策略转换为JSON字符串
    policy_json = json.dumps(policy, indent=2)

    # 设置向量桶策略
    result = client.put_vector_bucket_policy(
        vector_bucket_name=vector_bucket_name, account_id=account_id, policy=policy_json
    )

    # 打印操作结果
    print("设置向量桶IAM Policy成功")
    print("Request ID:", result.request_id)
    print("Status Code:", result.status_code)

except tos.exceptions.TosClientError as e:
    # 操作失败，捕获客户端异常，一般情况为非法请求参数或网络异常
    print("fail with client error, message:{}, cause: {}".format(e.message, e.cause))
except tos.exceptions.TosServerError as e:
    # 操作失败，捕获服务端异常，可从返回信息中获取详细错误信息
    print("fail with server error, code: {}".format(e.code))
    # request id 可定位具体问题，强烈建议日志中保存
    print("error with request id: {}".format(e.request_id))
    print("error with message: {}".format(e.message))
    print("error with http code: {}".format(e.status_code))
    print("error with ec: {}".format(e.ec))
    print("error with request url: {}".format(e.request_url))
except Exception as e:
    print("fail with unknown error: {}".format(e))
```

## GetVectorBucketPolicy
```
# 获取向量存储桶策略
import os
import tos

# 从环境变量获取 AK 和 SK 信息。
ak = os.getenv('TOS_ACCESS_KEY')
sk = os.getenv('TOS_SECRET_KEY')
# your endpoint 和 your region 填写Bucket 所在区域对应的Endpoint。# 以华北2(北京)为例，your endpoint 填写 https://tosvectors-cn-beijing.volces.com，your region 填写 cn-beijing。
endpoint = 'Provide your endpoint'
region = 'Provide your region'
vector_bucket_name = 'Provide your bucket name'
account_id = os.getenv('TOS_ACCOUNT_ID')

try:
    # 创建 VectorClient 对象，对向量的操作都通过 VectorClient 实现
    client = tos.VectorClient(ak, sk, endpoint, region)
    
    # 获取向量存储桶策略
    result = client.get_vector_bucket_policy(
        vector_bucket_name=vector_bucket_name,
        account_id=account_id  
    )
    
    # 打印获取策略成功的信息
    print('向量存储桶策略获取成功')
    print('Request ID: {}'.format(result.request_id))
    print('状态码: {}'.format(result.status_code))
    print('策略内容: {}'.format(result.policy))
    
except tos.exceptions.TosClientError as e:
    # 操作失败，捕获客户端异常，一般情况为非法请求参数或网络异常
    print('fail with client error, message:{}, cause: {}'.format(e.message, e.cause))
except tos.exceptions.TosServerError as e:
    # 操作失败，捕获服务端异常，可从返回信息中获取详细错误信息
    print('fail with server error, code: {}'.format(e.code))
    # request id 可定位具体问题，强烈建议日志中保存
    print('error with request id: {}'.format(e.request_id))
    print('error with message: {}'.format(e.message))
    print('error with http code: {}'.format(e.status_code))
    print('error with ec: {}'.format(e.ec))
    print('error with request url: {}'.format(e.request_url))
except Exception as e:
    print('fail with unknown error: {}'.format(e))
```
## DeleteVectorBucketPolicy
```
# 删除向量桶的IAM Policy
import os
import tos

# 从环境变量获取 AK 和 SK 信息。
ak = os.getenv('TOS_ACCESS_KEY')
sk = os.getenv('TOS_SECRET_KEY')
# your endpoint 和 your region 填写Bucket 所在区域对应的Endpoint。# 以华北2(北京)为例，your endpoint 填写 https://tosvectors-cn-beijing.volces.com，your region 填写 cn-beijing。
endpoint = 'Provide your endpoint'
region = 'Provide your region'
vector_bucket_name = 'Provide your bucket name'

# 从环境变量获取账户ID
account_id = os.getenv('TOS_ACCOUNT_ID')

try:
    # 创建 VectorClient 对象，对向量的操作都通过 VectorClient 实现
    client = tos.VectorClient(ak, sk, endpoint, region)
    
    # 删除向量存储桶的IAM策略
    # vector_bucket_name: 向量存储桶名称，需要提前创建
    # account_id: 账户ID，用于身份验证
    result = client.delete_vector_bucket_policy(
        vector_bucket_name=vector_bucket_name,
        account_id=account_id
    )
    
    # 打印删除结果信息
    print('成功删除向量桶IAM策略')
    print('Request ID:', result.request_id)
    print('Status Code:', result.status_code)
    print('Response Headers:', result.header)
    
except tos.exceptions.TosClientError as e:
    # 操作失败，捕获客户端异常，一般情况为非法请求参数或网络异常
    print('fail with client error, message:{}, cause: {}'.format(e.message, e.cause))
except tos.exceptions.TosServerError as e:
    # 操作失败，捕获服务端异常，可从返回信息中获取详细错误信息
    print('fail with server error, code: {}'.format(e.code))
    # request id 可定位具体问题，强烈建议日志中保存
    print('error with request id: {}'.format(e.request_id))
    print('error with message: {}'.format(e.message))
    print('error with http code: {}'.format(e.status_code))
    print('error with ec: {}'.format(e.ec))
    print('error with request url: {}'.format(e.request_url))
except Exception as e:
    print('fail with unknown error: {}'.format(e))
```
## CreateIndex
```
# 创建向量索引基本用法
import os
import tos

# 从环境变量获取 AK 和 SK 信息。
ak = os.getenv("TOS_ACCESS_KEY")
sk = os.getenv("TOS_SECRET_KEY")
# your endpoint 和 your region 填写Bucket 所在区域对应的Endpoint。# 以华北2(北京)为例，your endpoint 填写 https://tosvectors-cn-beijing.volces.com，your region 填写 cn-beijing。
endpoint = os.getenv("TOS_ENDPOINT")
region = os.getenv("TOS_REGION")
account_id = os.getenv("TOS_ACCOUNT_ID")
vector_bucket_name = os.getenv("TOS_BUCKET")

try:
    # 创建 VectorClient 对象，对向量的操作都通过 VectorClient 实现
    client = tos.VectorClient(ak, sk, endpoint, region)

    # 调用 create_index 创建向量索引
    response = client.create_index(
        account_id=account_id,
        vector_bucket_name=vector_bucket_name,
        index_name="examplevectorindex",
        data_type=tos.DataType.DataTypeFloat32,
        dimension=128,
        distance_metric=tos.DistanceMetricType.DistanceMetricCosine,
    )

    # 打印返回结果
    print("创建向量索引成功")
    print("request_id: {}".format(response.request_id))
    print("status_code: {}".format(response.status_code))

except tos.exceptions.TosClientError as e:
    # 操作失败，捕获客户端异常，一般情况为非法请求参数或网络异常
    print("fail with client error, message:{}, cause: {}".format(e.message, e.cause))
except tos.exceptions.TosServerError as e:
    # 操作失败，捕获服务端异常，可从返回信息中获取详细错误信息
    print("fail with server error, code: {}".format(e.code))
    # request id 可定位具体问题，强烈建议日志中保存
    print("error with request id: {}".format(e.request_id))
    print("error with message: {}".format(e.message))
    print("error with http code: {}".format(e.status_code))
    print("error with ec: {}".format(e.ec))
    print("error with request url: {}".format(e.request_url))
except Exception as e:
    print("fail with unknown error: {}".format(e))
```

## DeleteIndex
```
# 删除指定向量索引
import os
import tos

# 从环境变量获取 AK 和 SK 信息。
ak = os.getenv('TOS_ACCESS_KEY')
sk = os.getenv('TOS_SECRET_KEY')
# your endpoint 和 your region 填写Bucket 所在区域对应的Endpoint。# 以华北2(北京)为例，your endpoint 填写 https://tosvectors-cn-beijing.volces.com，your region 填写 cn-beijing。
endpoint = 'Provide your endpoint'
region = 'Provide your region'
vector_bucket_name = 'Provide your bucket name'

# 从环境变量获取账户ID和索引名称
account_id = os.getenv('TOS_ACCOUNT_ID')
index_name = 'examplevectorindex'

try:
    # 创建 VectorClient 对象，对向量的操作都通过 VectorClient 实现
    client = tos.VectorClient(ak, sk, endpoint, region)
    
    # 删除指定的向量索引
    # 参数说明：
    # - vector_bucket_name: 向量存储桶名称
    # - account_id: 账户ID，用于标识操作者的身份
    # - index_name: 要删除的向量索引名称
    result = client.delete_index(
        vector_bucket_name=vector_bucket_name,
        account_id=account_id,
        index_name=index_name
    )
    
    # 打印删除成功的信息
    print('向量索引删除成功')
    print('Request ID: {}'.format(result.request_id))
    print('状态码: {}'.format(result.status_code))
    print('索引名称: {}'.format(index_name))
    
except tos.exceptions.TosClientError as e:
    # 操作失败，捕获客户端异常，一般情况为非法请求参数或网络异常
    print('fail with client error, message:{}, cause: {}'.format(e.message, e.cause))
except tos.exceptions.TosServerError as e:
    # 操作失败，捕获服务端异常，可从返回信息中获取详细错误信息
    print('fail with server error, code: {}'.format(e.code))
    # request id 可定位具体问题，强烈建议日志中保存
    print('error with request id: {}'.format(e.request_id))
    print('error with message: {}'.format(e.message))
    print('error with http code: {}'.format(e.status_code))
    print('error with ec: {}'.format(e.ec))
    print('error with request url: {}'.format(e.request_url))
except Exception as e:
    print('fail with unknown error: {}'.format(e))
```

## GetIndex
```
# 获取指定向量索引的信息
import os
import tos

# 从环境变量获取 AK 和 SK 信息。
ak = os.getenv('TOS_ACCESS_KEY')
sk = os.getenv('TOS_SECRET_KEY')
# your endpoint 和 your region 填写Bucket 所在区域对应的Endpoint。# 以华北2(北京)为例，your endpoint 填写 https://tosvectors-cn-beijing.volces.com，your region 填写 cn-beijing。
endpoint = 'Provide your endpoint'
region = 'Provide your region'
account_id = os.getenv('TOS_ACCOUNT_ID')
vector_bucket_name = 'Provide your bucket name'

try:
    # 创建 VectorClient 对象，对向量的操作都通过 VectorClient 实现
    client = tos.VectorClient(ak, sk, endpoint, region)
    
    # 指定要获取的向量索引名称
    index_name = 'examplevectorindex'
    
    # 获取向量索引信息
    result = client.get_index(
        vector_bucket_name=vector_bucket_name,
        account_id=account_id,
        index_name=index_name
    )
    
    # 打印请求结果
    print('获取向量索引信息成功')
    print('Request ID:', result.request_id)
    print('Status Code:', result.status_code)
    
    # 打印索引详细信息
    if result.index:
        print('\n索引详细信息:')
        print('索引名称:', result.index.index_name)
        print('数据类型:', result.index.data_type)
        print('向量维度:', result.index.dimension)
        print('距离度量方式:', result.index.distance_metric)
        print('创建时间:', result.index.creation_time)
        print('索引TRN:', result.index.index_trn)
        
        # 打印元数据配置信息
        if result.index.metadata_configuration:
            print('非可过滤元数据键:', result.index.metadata_configuration.non_filterable_metadata_keys)
    
except tos.exceptions.TosClientError as e:
    # 操作失败，捕获客户端异常，一般情况为非法请求参数或网络异常
    print('fail with client error, message:{}, cause: {}'.format(e.message, e.cause))
except tos.exceptions.TosServerError as e:
    # 操作失败，捕获服务端异常，可从返回信息中获取详细错误信息
    print('fail with server error, code: {}'.format(e.code))
    # request id 可定位具体问题，强烈建议日志中保存
    print('error with request id: {}'.format(e.request_id))
    print('error with message: {}'.format(e.message))
    print('error with http code: {}'.format(e.status_code))
    print('error with ec: {}'.format(e.ec))
    print('error with request url: {}'.format(e.request_url))
except Exception as e:
    print('fail with unknown error: {}'.format(e))
```

## ListIndexes
```
# 列举向量索引基本用法
import os
import tos

# 从环境变量获取 AK 和 SK 信息。
ak = os.getenv('TOS_ACCESS_KEY')
sk = os.getenv('TOS_SECRET_KEY')
# your endpoint 和 your region 填写Bucket 所在区域对应的Endpoint。# 以华北2(北京)为例，your endpoint 填写 https://tosvectors-cn-beijing.volces.com，your region 填写 cn-beijing。
endpoint = 'Provide your endpoint'
region = 'Provide your region'
account_id = os.getenv('TOS_ACCOUNT_ID')
vector_bucket_name = 'Provide your bucket name'

try:
    # 创建 VectorClient 对象，对向量的操作都通过 VectorClient 实现
    client = tos.VectorClient(ak, sk, endpoint, region)
    
    # 列举指定向量存储桶中的所有索引
    result = client.list_indexes(
        vector_bucket_name=vector_bucket_name,  # 向量存储桶名称
        account_id=account_id                   # 账户ID
    )
    
    # 打印请求基本信息
    print(f'Request ID: {result.request_id}')
    print(f'Status Code: {result.status_code}')
    print(f'Indexes Count: {len(result.indexes)}')
    
    # 遍历并打印索引信息
    if result.indexes:
        print('\nIndex Details:')
        for index in result.indexes:
            print(f'  Index Name: {index.index_name}')
            print(f'  Index TRN: {index.index_trn}')
            print(f'  Vector Bucket: {index.vector_bucket_name}')
            print(f'  Creation Time: {index.creation_time}')
    
    # 如果有分页令牌，说明还有更多的索引
    if result.next_token:
        print(f'\nNext Token: {result.next_token}')
        print('Note: There are more indexes available. Use next_token for pagination.')
        
except tos.exceptions.TosClientError as e:
    # 操作失败，捕获客户端异常，一般情况为非法请求参数或网络异常
    print('fail with client error, message:{}, cause: {}'.format(e.message, e.cause))
except tos.exceptions.TosServerError as e:
    # 操作失败，捕获服务端异常，可从返回信息中获取详细错误信息
    print('fail with server error, code: {}'.format(e.code))
    # request id 可定位具体问题，强烈建议日志中保存
    print('error with request id: {}'.format(e.request_id))
    print('error with message: {}'.format(e.message))
    print('error with http code: {}'.format(e.status_code))
    print('error with ec: {}'.format(e.ec))
    print('error with request url: {}'.format(e.request_url))
except Exception as e:
    print('fail with unknown error: {}'.format(e))
```

## PutVectors
```
# 向量索引中批量插入向量
import os
import tos

# 从环境变量获取 AK 和 SK 信息。
ak = os.getenv('TOS_ACCESS_KEY')
sk = os.getenv('TOS_SECRET_KEY')
# your endpoint 和 your region 填写Bucket 所在区域对应的Endpoint。# 以华北2(北京)为例，your endpoint 填写 https://tosvectors-cn-beijing.volces.com，your region 填写 cn-beijing。
endpoint = 'Provide your endpoint'
region = 'Provide your region'
account_id = os.getenv('TOS_ACCOUNT_ID')
vector_bucket_name = 'Provide your bucket name'

try:
    # 创建 VectorClient 对象，对向量的操作都通过 VectorClient 实现
    client = tos.VectorClient(ak, sk, endpoint, region)
    
    # 准备向量数据 - 创建3个128维的float32向量
    vectors = []
    for i in range(3):
        # 创建向量数据，每个向量128维
        vector_data = tos.models2.VectorData(
            float32=[float(x) for x in range(128)]  # 128维向量数据
        )
        
        # 创建向量对象
        vector = tos.models2.Vector(
            key=f'vector-{i}',  # 向量唯一键
            data=vector_data    # 向量数据
        )
        vectors.append(vector)
    
    # 指定索引名称
    index_name = 'test'
    
    # 批量插入向量到向量索引中
    result = client.put_vectors(
        vector_bucket_name=vector_bucket_name,
        account_id=account_id,
        index_name=index_name,
        vectors=vectors
    )
    
    # 打印操作结果
    print('批量插入向量成功')
    print('Request ID:', result.request_id)
    print('Status Code:', result.status_code)
    print('成功插入向量数量:', len(vectors))
    
except tos.exceptions.TosClientError as e:
    # 操作失败，捕获客户端异常，一般情况为非法请求参数或网络异常
    print('fail with client error, message:{}, cause: {}'.format(e.message, e.cause))
except tos.exceptions.TosServerError as e:
    # 操作失败，捕获服务端异常，可从返回信息中获取详细错误信息
    print('fail with server error, code: {}'.format(e.code))
    # request id 可定位具体问题，强烈建议日志中保存
    print('error with request id: {}'.format(e.request_id))
    print('error with message: {}'.format(e.message))
    print('error with http code: {}'.format(e.status_code))
    print('error with ec: {}'.format(e.ec))
    print('error with request url: {}'.format(e.request_url))
except Exception as e:
    print('fail with unknown error: {}'.format(e))
```

## DeleteVectors
```
# 批量删除向量
import os
import tos

# 从环境变量获取 AK 和 SK 信息。
ak = os.getenv('TOS_ACCESS_KEY')
sk = os.getenv('TOS_SECRET_KEY')
# your endpoint 和 your region 填写Bucket 所在区域对应的Endpoint。# 以华北2(北京)为例，your endpoint 填写 https://tosvectors-cn-beijing.volces.com，your region 填写 cn-beijing。
endpoint = 'Provide your endpoint'
region = 'Provide your region'
vector_bucket_name = 'Provide your bucket name'
 # 获取账户ID（通常可以从环境变量或配置中获取）
account_id = os.getenv('TOS_ACCOUNT_ID')

try:
    # 创建 VectorClient 对象，对向量的操作都通过 VectorClient 实现
    client = tos.VectorClient(ak, sk, endpoint, region)
    
    # 设置向量索引名称和要删除的向量键列表
    index_name = 'test'
    # 要删除的向量键列表，这些键对应之前写入的向量数据
    keys_to_delete = [
        'vector-key-1',
        'vector-key-2', 
        'vector-key-3'
    ]
    
    # 调用 delete_vectors 方法批量删除向量数据
    result = client.delete_vectors(
        vector_bucket_name=vector_bucket_name,
        account_id=account_id,
        index_name=index_name,
        keys=keys_to_delete
    )
    
    # 打印删除操作的结果信息
    print('向量删除成功')
    print('请求ID: {}'.format(result.request_id))
    print('状态码: {}'.format(result.status_code))
    print('响应头: {}'.format(result.header))
    
except tos.exceptions.TosClientError as e:
    # 操作失败，捕获客户端异常，一般情况为非法请求参数或网络异常
    print('fail with client error, message:{}, cause: {}'.format(e.message, e.cause))
except tos.exceptions.TosServerError as e:
    # 操作失败，捕获服务端异常，可从返回信息中获取详细错误信息
    print('fail with server error, code: {}'.format(e.code))
    # request id 可定位具体问题，强烈建议日志中保存
    print('error with request id: {}'.format(e.request_id))
    print('error with message: {}'.format(e.message))
    print('error with http code: {}'.format(e.status_code))
    print('error with ec: {}'.format(e.ec))
    print('error with request url: {}'.format(e.request_url))
except Exception as e:
    print('fail with unknown error: {}'.format(e))
```

## GetVectors

```
# 根据向量key批量查询向量
import os
import tos

# 从环境变量获取 AK 和 SK 信息。
ak = os.getenv('TOS_ACCESS_KEY')
sk = os.getenv('TOS_SECRET_KEY')
# your endpoint 和 your region 填写Bucket 所在区域对应的Endpoint。# 以华北2(北京)为例，your endpoint 填写 https://tosvectors-cn-beijing.volces.com，your region 填写 cn-beijing。
endpoint = 'Provide your endpoint'
region = 'Provide your region'
vector_bucket_name = 'Provide your bucket name'
account_id = os.getenv('TOS_ACCOUNT_ID')

try:
    # 创建 VectorClient 对象，对向量的操作都通过 VectorClient 实现
    client = tos.VectorClient(ak, sk, endpoint, region)
    
    # 设置查询参数
    index_name = "test"  # 向量索引名称
    keys = ["product-vector-0", "product-vector-1", "product-vector-2"]  # 要查询的向量键列表
    
    # 调用 get_vectors API 批量查询向量（不返回数据和元数据）
    result = client.get_vectors(
        vector_bucket_name=vector_bucket_name,
        account_id=account_id,
        index_name=index_name,
        keys=keys,
        return_data=True,  # 返回向量数据
        return_metadata=True  # 返回向量元数据
    )
    
    # 打印查询结果
    print(f"查询成功，状态码: {result.status_code}")
    print(f"返回向量数量: {len(result.vectors)}")
    
    # 遍历返回的向量结果
    for vector in result.vectors:
        print(f"向量键: {vector.key}")
        print(f"向量数据: {vector.data.float32}")
        print(f"向量元数据: {vector.metadata}")
        print("---")
    
except tos.exceptions.TosClientError as e:
    # 操作失败，捕获客户端异常，一般情况为非法请求参数或网络异常
    print('fail with client error, message:{}, cause: {}'.format(e.message, e.cause))
except tos.exceptions.TosServerError as e:
    # 操作失败，捕获服务端异常，可从返回信息中获取详细错误信息
    print('fail with server error, code: {}'.format(e.code))
    # request id 可定位具体问题，强烈建议日志中保存
    print('error with request id: {}'.format(e.request_id))
    print('error with message: {}'.format(e.message))
    print('error with http code: {}'.format(e.status_code))
    print('error with ec: {}'.format(e.ec))
    print('error with request url: {}'.format(e.request_url))
except Exception as e:
    print('fail with unknown error: {}'.format(e))
```


## ListVectors
```
# 列举向量基本用法
import os
import tos

# 从环境变量获取 AK 和 SK 信息。
ak = os.getenv('TOS_ACCESS_KEY')
sk = os.getenv('TOS_SECRET_KEY')
# your endpoint 和 your region 填写Bucket 所在区域对应的Endpoint。# 以华北2(北京)为例，your endpoint 填写 https://tosvectors-cn-beijing.volces.com，your region 填写 cn-beijing。
endpoint = 'Provide your endpoint'
region = 'Provide your region'
vector_bucket_name = 'Provide your bucket name'
account_id = os.getenv('TOS_ACCOUNT_ID')

try:
    # 创建 VectorClient 对象，对向量的操作都通过 VectorClient 实现
    client = tos.VectorClient(ak, sk, endpoint, region)
    
    # 设置向量索引名称和账号ID
    index_name = "test"

    
    # 列举向量并返回数据和元数据
    print("\n=== 列举向量（返回数据和元数据）===")
    result_with_data = client.list_vectors(
        vector_bucket_name=vector_bucket_name,
        index_name=index_name,
        account_id=account_id,
        return_data=True,      # 返回向量数据
        return_metadata=True     # 返回向量元数据
    )
    
    print(f"请求ID: {result_with_data.request_id}")
    print(f"返回向量数量: {len(result_with_data.vectors)}")
    
    # 打印详细向量信息
    if result_with_data.vectors:
        vector = result_with_data.vectors[0]
        print(f"第一个向量的详细信息:")
        print(f"  Key: {vector.key}")
        if vector.data and vector.data.float32:
            print(f"  Data长度: {len(vector.data.float32)}")
            print(f"  Data的值: {vector.data.float32}")
        if vector.metadata:
            print(f"  Metadata: {vector.metadata}")
    
    # 限制返回数量
    print("\n=== 限制返回数量 ===")
    result_limited = client.list_vectors(
        vector_bucket_name=vector_bucket_name,
        index_name=index_name,
        account_id=account_id,
        max_results=3  # 最多返回3条向量
    )
    
    print(f"请求ID: {result_limited.request_id}")
    print(f"返回向量数量: {len(result_limited.vectors)}")
    
except tos.exceptions.TosClientError as e:
    # 操作失败，捕获客户端异常，一般情况为非法请求参数或网络异常
    print('fail with client error, message:{}, cause: {}'.format(e.message, e.cause))
except tos.exceptions.TosServerError as e:
    # 操作失败，捕获服务端异常，可从返回信息中获取详细错误信息
    print('fail with server error, code: {}'.format(e.code))
    # request id 可定位具体问题，强烈建议日志中保存
    print('error with request id: {}'.format(e.request_id))
    print('error with message: {}'.format(e.message))
    print('error with http code: {}'.format(e.status_code))
    print('error with ec: {}'.format(e.ec))
    print('error with request url: {}'.format(e.request_url))
except Exception as e:
    print('fail with unknown error: {}'.format(e))
```

## QueryVectors
```
# 查询距离最近TopK向量
import os
import random
import tos
from tos.models2 import VectorData

# 从环境变量获取 AK 和 SK 信息。
ak = os.getenv('TOS_ACCESS_KEY')
sk = os.getenv('TOS_SECRET_KEY')
# your endpoint 和 your region 填写Bucket 所在区域对应的Endpoint。# 以华北2(北京)为例，your endpoint 填写 https://tosvectors-cn-beijing.volces.com，your region 填写 cn-beijing。
endpoint = 'Provide your endpoint'
region = 'Provide your region'
account_id = os.getenv('TOS_ACCOUNT_ID')
vector_bucket_name = 'Provide your bucket name'
index_name = "testindex"

try:
    # 创建 VectorClient 对象，对向量的操作都通过 VectorClient 实现
    client = tos.VectorClient(ak, sk, endpoint, region)
    
    # 创建查询向量数据，维度为128的浮点向量
    query_vector = VectorData(float32=[float(random.random()) for _ in range(128)])
    
    # 执行向量搜索，返回最相似的5个向量
    result = client.query_vectors(
        vector_bucket_name=vector_bucket_name,
        account_id=account_id,
        index_name=index_name,
        query_vector=query_vector,
        top_k=5,  # 返回最相似的5个向量
        return_distance=True,  # 返回向量距离
        return_metadata=True   # 返回向量元数据
    )
    
    # 打印请求信息
    print('Request ID: {}'.format(result.request_id))
    print('Status Code: {}'.format(result.status_code))
    print('Found {} similar vectors'.format(len(result.vectors)))
    
    # 打印搜索结果
    for i, vector in enumerate(result.vectors):
        print('\nTop {}:'.format(i + 1))
        print('  Key: {}'.format(vector.key))
        print('  Distance: {:.4f}'.format(vector.distance))
        print('  Metadata: {}'.format(vector.metadata))
        if vector.data and vector.data.float32:
            print('  Vector dimension: {}'.format(len(vector.data.float32)))

except tos.exceptions.TosClientError as e:
    # 操作失败，捕获客户端异常，一般情况为非法请求参数或网络异常
    print('fail with client error, message:{}, cause: {}'.format(e.message, e.cause))
except tos.exceptions.TosServerError as e:
    # 操作失败，捕获服务端异常，可从返回信息中获取详细错误信息
    print('fail with server error, code: {}'.format(e.code))
    # request id 可定位具体问题，强烈建议日志中保存
    print('error with request id: {}'.format(e.request_id))
    print('error with message: {}'.format(e.message))
    print('error with http code: {}'.format(e.status_code))
    print('error with ec: {}'.format(e.ec))
    print('error with request url: {}'.format(e.request_url))
except Exception as e:
    print('fail with unknown error: {}'.format(e))
```