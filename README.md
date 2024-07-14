
# 摘要
本文介绍了一个使用GraphRAG和通义千问API分析川普枪击事件的方法。由于GraphRAG目前只支持OpenAI API，采用One-Api将通义转成了OpenAI API风格的接口。

# GraphRAG 介绍
&nbsp;&nbsp;微软最近开源了名为GraphRAG的技术，这是一种结合了文本提取、网络分析以及大型语言模型（LLM）提示和总结的端到端系统，旨在丰富地理解文本数据集。GraphRAG代表了一种结构化、层次化的检索增强生成（RAG）方法，与传统的基于纯文本片段的语义搜索方法不同。  
&nbsp;&nbsp;GraphRAG通过从原始文本中提取知识图谱，并构建社区层次结构，生成这些社区的摘要，然后在执行基于RAG的任务时利用这些结构。这种方法在处理私有数据集时，特别是在问答性能方面，显示出了对复杂信息的推理能力，相较于传统的RAG技术有了显著提升。  
&nbsp;&nbsp;在GitHub上推出后，GraphRAG项目迅速获得了社区的广泛关注，获得了2700颗star。微软在其博客上介绍了GraphRAG，并指出它在处理私有数据集时，如企业的专有研究、商业文件或通讯等，提供了显著的性能提升。在大规模播客和新闻数据集上进行的测试显示，在全面性、多样性和赋权性方面，GraphRAG都优于朴素RAG技术。  
&nbsp;&nbsp;GraphRAG的开源地址为：https://github.com/microsoft/graphrag，开发者们对此表现出极大的兴趣，并期待尝试这一技术。  
&nbsp;&nbsp;与传统的RAG方法相比，GraphRAG在处理需要全局理解的海量数据查询时，能够更好地捕捉文本中的复杂联系和交互，从而增强其生成和检索能力。实验结果表明，GraphRAG在全面性和多样性测试上超越了Naive RAG等方法，并且具有较低的资源需求。  
&nbsp;&nbsp;总的来说，GraphRAG是微软在增强大语言模型能力方面的一大进步，它通过构建知识图谱和图机器学习，极大地增强了LLM在处理私有数据时的性能，尤其是在跨大型数据集的复杂语义问题推理能力方面。  

# 环境部署
``` shell
git clone git@github.com:microsoft/graphrag.git

cd graphrag

poetry install ## 测试直接采用poetry运行本地文件, wheel包目前自测下来有坑, 直接pip安装有问题

## 新建graphrag 项目
poetry run poe index --root ~/trumprag/ --init

## 创建输入
mkdir -p ~/trumprag/input


## 上传新闻稿到input目录下
## 来源: https://finance.eastmoney.com/a/202407143130425967.html

## 本地起one-api服务
## https://github.com/songquanpeng/one-api
## one-api部署手册见：https://github.com/songquanpeng/one-api?tab=readme-ov-file#%E6%89%8B%E5%8A%A8%E9%83%A8%E7%BD%B2

##修改setting.yaml
## 见https://github.com/flyrae/trumprag/blob/main/settings.yaml

```

# 运行

1. 运行Indexing Engine  
`poetry run poe index --root ~/trumprag/`  
最后得到下面的日志则说明成功执行。
``` log
⠙ GraphRAG Indexer
├── Loading Input (text) - 1 files loaded (0 filtered) ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 100% 0:00:00 0:00:00
├── create_base_text_units
├── create_base_extracted_entities
├── create_summarized_entities
├── create_base_entity_graph
├── create_final_entities
├── create_final_nodes
├── create_final_communities
├── join_text_units_to_entity_ids
├── create_final_relationships
├── join_text_units_to_relationship_ids
├── create_final_community_reports
├── create_final_text_units
├── create_base_documents
└── create_final_documents
🚀 All workflows completed successfully.

```

2. 运行查询  
` poetry run poe query --root ~/trumprag/ --method global "特朗普发生了什么事"`   

``` log
INFO: Reading settings from /root/trumprag/settings.yaml
creating llm client with {'api_key': 'REDACTED,len=35', 'type': "openai_chat", 'model': 'qwen2-72b-instruct', 'max_tokens': 2000, 'temperature': 0.0, 'top_p': 0.9, 'n': 1, 'request_timeout': 180.0, 'api_base': 'https://dashscope.aliyuncs.com/compatible-mode/v1', 'api_version': None, 'organization': None, 'proxy': None, 'cognitive_services_endpoint': None, 'deployment_name': None, 'model_supports_json': False, 'tokens_per_minute': 300000, 'requests_per_minute': 30, 'max_retries': 3, 'max_retry_wait': 10.0, 'sleep_on_rate_limit_recommendation': True, 'concurrent_requests': 1}

SUCCESS: Global Search Response: # 特朗普在宾夕法尼亚州竞选集会上遭遇枪击事件的综合报告

## 事件概述
在宾夕法尼亚州巴特勒市的一次竞选集会上，特朗普遭遇了枪击事件，这一突发事件导致了人员伤亡，迫使集会被中断，可能会引发政治和社会动荡 [Data: Reports (2)]。

## 安全响应
特勤局迅速采取行动，有效地保护并疏散了特朗普，确保了他的个人安全。这一专业和及时的反应避免了更严重的后果 [Data: Reports (2)]。

## 总统关注
作为现任总统，拜登接收到关于这次枪击事件的初步简报，表明政府正在密切关注事态发展 [Data: Reports (2)]。

## 目击者证言
罗恩·穆斯作为现场目击者，提供了关键证词，这对于事件的后续调查将起到重要作用 [Data: Reports (2)]。

## 地方安全疑虑
此次事件的发生地点巴特勒市，其公共安全和危机应对能力受到了公众的质疑，这可能影响到当地民众对政府的信任度以及未来的公共活动安排 [Data: Reports (2)]。

综上所述，特朗普在巴特勒市的竞选集会上遭遇的枪击事件不仅是一个严重的安全问题，还牵涉到了政治稳定、政府响应能力和地方治安等多方面议题。
```


