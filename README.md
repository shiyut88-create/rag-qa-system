# 📚 RAG 文档问答系统

> 基于 RAG（检索增强生成）架构构建的智能文档问答系统，支持上传多种格式文档后进行自然语言问答。

## 项目背景

大语言模型虽然具备强大的语言理解能力，但存在两个核心局限：知识存在截止日期，以及无法获取用户的私有文档内容。本项目基于 RAG 架构解决这一问题——不修改模型本身，而是在用户提问时实时检索相关文档片段，将其作为上下文注入 Prompt，让模型基于指定文档进行回答，从而实现准确、可追溯的私有知识库问答。

## 功能特点

- 📄 支持上传 PDF、Word（.docx）、TXT 多种格式文档
- 🔍 基于语义向量检索，准确匹配相关内容（优于关键字搜索）
- 📌 每条回答显示来源文件名与页码，结果可追溯
- 💬 支持多轮对话，模型记忆上下文，可追问细节
- ⚙️ 可调节检索块数量（k值），灵活平衡回答质量与速度
- 🗑️ 支持一键清空对话记录

## 技术栈

| 模块 | 技术 |
|------|------|
| 文档解析 | PyMuPDF、python-docx |
| 文本切块 | LangChain RecursiveCharacterTextSplitter |
| 向量化 | sentence-transformers（paraphrase-multilingual-MiniLM-L12-v2）|
| 向量数据库 | Chroma |
| 大语言模型 | DeepSeek API（兼容 OpenAI 接口）|
| 前端界面 | Streamlit |

## 系统架构与实现原理

本系统分为两个阶段：

**阶段一：知识库构建（离线）**


用户上传文档（PDF / Word / TXT）
↓
文档解析，提取纯文本
↓
RecursiveCharacterTextSplitter 切块（每块500字，重叠50字）
↓
sentence-transformers 向量化每个文本块
↓
存入 Chroma 本地向量数据库


**阶段二：智能问答（在线）**


用户输入问题
↓
问题向量化
↓
在 Chroma 中语义检索最相关的 k 个文本块
↓
将检索结果 + 对话历史 + 问题拼接成 Prompt
↓
DeepSeek 大模型生成回答
↓
返回答案 + 来源页码


**为什么用向量检索而不是关键字搜索？**

向量检索基于语义相似度，能匹配意思相近但用词不同的内容。例如用户问"如何申请退款"，文档中写的是"办理退货流程"，关键字搜索无法匹配，向量检索则可以准确找到。

## 快速开始

### 环境要求

- Python 3.9+
- DeepSeek API Key（[申请地址](https://platform.deepseek.com/)）

### 1. 克隆项目

```bash
git clone https://github.com/你的用户名/rag-qa-system.git
cd rag-qa-system


2. 安装依赖

pip install streamlit langchain langchain-community langchain-text-splitters
pip install pymupdf python-docx sentence-transformers chromadb openai


3. 启动应用

streamlit run main.py


4. 使用步骤

	1.	在左侧侧边栏上传文档（支持 PDF / Word / TXT）
	2.	点击「🔄 构建知识库」按钮，等待解析完成
	3.	在底部输入框输入问题开始问答
	4.	点击回答下方「📌 来源」查看答案出处
	5.	可拖动侧边栏滑块调整检索块数量

项目截图
**主页界面**
![主页界面](images/01_home.png)

**上传文档并构建知识库**
![上传文档](images/02_upload.png)

**问答效果与来源展示**
![问答效果](images/03_answer.png)

**多轮对话记忆**
![多轮对话](images/04_history.png)




未来计划

	•	支持在线更新知识库，无需重启应用
	•	增加文档管理页面，可查看和删除已上传文档
	•	接入更多大模型（GPT-4、Claude 等）供用户切换
	•	优化切块策略，支持按标题层级切块（适合结构化文档）
	•	增加回答质量评分功能

作者

谭诗语 | shiyut88@gmail.com 


---


