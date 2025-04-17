# 🎁 雅戈尔智能导购助手（基于 FastAPI + Gradio + LLM）

## 📘 项目概述

本项目搭建了一套面向零售场景的轻量级智能导购助手，结合 **NIM** 调用大模型，使用 **FAISS** 进行向量检索，前端采用 **Gradio**，后端基于 **FastAPI** 异步框架。通过端到端的异步流式交互体验，实现多种业务场景的智能辅助功能：

- 🎂 **会员生日祝福**  
- 🌦️ **门店天气提醒**  
- 🎉 **节日祝福生成**  
- 🛍️ **商品匹配推荐**  

此外，该项目还与 MySQL 数据库对接并调用外部天气接口，给大模型的智能生成提供外部信息。

---

## 📂 项目结构与脚本功能

- **`page.py`（前端交互脚本）**  
  使用 Gradio 快速搭建用户界面，包含功能模式下拉、编码与附加信息输入、生成结果输出。前端负责捕获用户输入、调用后端接口，并以流式方式实时显示文本增量。

- **`script.py`（后端业务逻辑）**  
  基于 FastAPI 异步框架，加载预构建的 FAISS 索引与 ID 映射，并通过 Aiomysql 建立数据库连接池。服务暴露 `/script` POST 接口，根据不同 `mode` 调度相应业务流程，调用 AsyncOpenAI 接口执行大模型生成或推理，最终以 StreamingResponse 返回流式结果。

- **`data.ipynb`（向量嵌入构建）**  
  对业务文本数据进行清洗与抽样，通过 NIM 嵌入模型生成向量，并使用 FAISS FlatL2 索引构建离线检索库，保存索引文件与文本 ID 映射，为商品匹配提供底层检索能力。

---

## 🔧 技术架构

- **后端框架**：FastAPI（异步路由与流式传输）  
- **前端界面**：Gradio（快速搭建与流式展示）  
- **大模型服务**：NVIDIA API（流式生成）  
- **数据库支持**：Aiomysql（异步数据库连接池）  
- **天气接口**：Httpx（异步请求天气数据）  
- **向量检索**：FAISS（顾客信息相似检索）

---

## 📦 功能模块说明

### 🎂 模式 0：生日祝福生成
- 从数据库查询会员 `姓名`、`性别`、`生日`
- 结合会员信息、当前日期和可选补充描述，拼装 Prompt  
- 调用 LLaMA-3.1-Nemotron-Ultra 模型生成含 Emoji 的个性化中文祝福

### 🌦️ 模式 1：天气提醒生成
- 根据门店编码获取 `城市行政编码`  
- 调用彩云天气 API 获取实时天气简报  
- 构建说明性 Prompt，调用大模型生成安全出行、穿衣建议等提醒话术

### 🎉 模式 2：节日祝福推送
- 查询会员信息并计算年龄  
- 识别用户输入的节日名称或者默认最近节日  
- 结合节日风俗与用户画像，调用大模型生成富有节日氛围的祝福

### 🛍️ 模式 3：商品匹配推荐
- 查询会员 `性别`、`年龄` 与附加信息组成特征字符串  
- 调用 NIM 嵌入接口生成特征字符串的向量 
- 在 FAISS 索引中检索 Top-K 相似记录  
- 构建推荐 Prompt，调用 DeepSeek-R1 推理模型输出商品类别、风格、颜色等推荐要点

---

## 💡 系统流程图

```text
Gradio 界面输入
    │
    ▼
FastAPI 异步接口
    │
    ├─ 查询 MySQL 数据库
    ├─ 请求天气 API
    ├─ Prompt 拼装 
    └─ OpenAI SDK 流式调用
    │
    ▼
StreamingResponse 流式返回
    │
    ▼
Gradio 实时渲染增量文本
```

---

## 🧠 技术亮点

1. **异步全链路**：FastAPI + Asyncio + HTTPX + Aiomysql + StreamingResponse，实现非阻塞数据库与模型调用，保证高并发和实时交互。  
2. **向量检索**：在商品匹配中先检索类似顾客记录再生成，大幅提升推荐相关性与个性化程度。  
3. **流式输出体验**：客户端可实时接收增量文本，提升用户交互流畅度，缓解导购因等待而产生的烦躁情绪。  
4. **模块化可扩展**：预留模块增加空间，可快速拓展更多业务场景。

---

## 🎯 项目成果与收获

- **技术掌握**：熟悉 NIM 集成 OpenAI 接口使用、FAISS 索引构建与检索流程、异步 Python 编程模式。  
- **性能表现**：向量检索耗时和大模型流式输出延迟可控，满足简单演示需求和实际业务应用情况。

---

## ⚙️ 挑战与优化空间

- **大模型调优**：根据更加详细的业务逻辑，优化 Prompt 结构，提升生成准确度。  
- **检索规模扩展**：对接专业向量数据库，支持百万级样本检索，保证性能与稳定性。  
- **业务闭环**：接入日志与用户反馈系统，构建自定义程度更高的智能助手。

---

## 🚀 未来展望

- 多模态融合：结合实际商品图像与文本描述，实现更丰富的推荐维度。  
- 增量索引更新：使用脚本定期批量插入新样本，无需重建全量索引。  
- 跨平台接入：通过 API 支持钉钉、微信小程序及企业内部系统对接。  
- 智能运营：结合用户行为与反馈，自动化调整营销策略与推荐逻辑。

