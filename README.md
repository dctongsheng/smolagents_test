# arXiv搜索服务

这是一个基于MCP（Model Context Protocol）的arXiv论文搜索服务。

## 功能

- 根据关键词搜索arXiv论文
- 获取指定ID的论文详细信息

## 安装

```bash
pip install -r requirements.txt
```

## 使用方法

### 启动服务器

```bash
python arxiv_server.py
```

服务器将在本地启动，使用SSE传输协议。

### 使用客户端

可以通过以下方式使用客户端：

#### 1. 命令行工具

最简单的使用方式是通过命令行工具：

```bash
# 搜索论文
python arxiv_cli.py search "quantum computing" --max-results 3

# 显示详细信息
python arxiv_cli.py search "quantum computing" --detailed

# 以JSON格式输出
python arxiv_cli.py search "quantum computing" --json

# 获取特定论文详情
python arxiv_cli.py details 2307.09288

# 连接到自定义服务器
python arxiv_cli.py --server http://your-server:8000 search "quantum computing"
```

命令行工具帮助信息：

```bash
# 显示帮助信息
python arxiv_cli.py --help

# 显示搜索命令帮助
python arxiv_cli.py search --help

# 显示详情命令帮助
python arxiv_cli.py details --help
```

#### 2. 同步客户端

```python
from arxiv_client import ArxivClient

# 创建客户端实例（默认连接到http://localhost:8000）
client = ArxivClient()

# 或者指定服务器URL
# client = ArxivClient("http://your-server-url:8000")

# 搜索论文
papers = client.search_arxiv("quantum computing", max_results=5, sort_by="relevance")
for paper in papers:
    print(f"标题: {paper['title']}")
    print(f"作者: {', '.join(paper['authors'])}")
    print(f"PDF链接: {paper['pdf_url']}")
    print("---")

# 获取特定论文详情
paper_id = "2307.09288"  # arXiv论文ID
paper = client.get_paper_details(paper_id)
print(f"标题: {paper['title']}")
print(f"摘要: {paper['summary']}")
```

#### 3. 异步客户端

```python
import asyncio
from arxiv_async_client import AsyncArxivClient

async def main():
    # 创建异步客户端实例
    client = AsyncArxivClient()
    
    try:
        # 异步搜索论文
        papers = await client.search_arxiv("quantum computing", max_results=5)
        for paper in papers:
            print(f"标题: {paper['title']}")
        
        # 异步获取论文详情
        if papers:
            paper_id = papers[0]['entry_id'].split('/')[-1]
            details = await client.get_paper_details(paper_id)
            print(f"详情: {details['title']}")
    finally:
        # 关闭客户端连接
        await client.close()

# 运行异步函数
asyncio.run(main())
```

#### 4. 运行示例脚本

同步客户端示例:
```bash
python arxiv_client.py
```

异步客户端示例:
```bash
python arxiv_async_client.py
```

### API功能

1. `search_arxiv(query, max_results=10, sort_by="submitted_date")`
   - 搜索arXiv论文
   - 参数:
     - query: 搜索关键词
     - max_results: 返回结果的最大数量，默认为10
     - sort_by: 排序方式，可选值为"submitted_date"(提交日期)、"relevance"(相关性)

2. `get_paper_details(paper_id)`
   - 获取指定ID的arXiv论文详细信息
   - 参数:
     - paper_id: arXiv论文ID

## 项目结构

- `arxiv_server.py`: MCP服务器实现，提供arXiv搜索API
- `arxiv_client.py`: MCP同步客户端实现
- `arxiv_async_client.py`: MCP异步客户端实现
- `arxiv_cli.py`: 命令行工具
- `requirements.txt`: 项目依赖
- `README.md`: 项目说明文档