# Demo 03: Building a Reactive Agent Workflow with LangGraph and Tavily

## Overview

This demo builds a **ReAct-style AI agent** using [LangGraph](https://github.com/langchain-ai/langgraph) integrated with [Tavily](https://tavily.com/) for real-time web search. The agent follows a **Reason → Act → Observe** loop — it reasons about the user's query, decides whether external data is needed, calls a search tool if necessary, and synthesizes the results into a final answer.

## Scenario

A product lead at a fintech startup wants to monitor global developments in real-time fraud detection methods for UPI systems. Manually tracking cybersecurity research and regulatory changes is inefficient. This demo automates the workflow: an agent uses a search tool to gather the latest findings and produces a summarized response.

## Architecture

```
User Query
    │
    ▼
┌──────────┐    tool call     ┌──────────────┐
│  LLM     │ ──────────────►  │ Tavily Search│
│ (ReAct)  │ ◄──────────────  │   Tool       │
└──────────┘   search results └──────────────┘
    │
    ▼
Final Answer
```

The agent graph has two nodes:
- **Model Node** — The LLM reasons about the query and decides whether to call a tool.
- **Tool Node** — Executes the Tavily web search and returns results to the model.

## Prerequisites

| Requirement | Details |
|---|---|
| Python | 3.10 or higher |
| API Keys | At least one LLM provider key + Tavily API key |
| Environment | Jupyter Notebook or VS Code with Jupyter extension |

## Setup

### 1. Create a `.env` file

Create a `.env` file in this directory with your API keys:

```env
TAVILY_API_KEY=tvly-your-key-here

# Choose at least one LLM provider:
GROQ_API_KEY=gsk_your-key-here
HF_API_KEY=hf_your-key-here

# Optional — Azure OpenAI:
AZURE_OPENAI_API_KEY=your-key-here
AZURE_OPENAI_ENDPOINT=https://your-endpoint.openai.azure.com/
AZURE_OPENAI_API_VERSION=2024-12-01-preview
AZURE_OPENAI_DEPLOYMENT=gpt-4o-mini
```

### 2. Install dependencies

Run the first cell in the notebook, or manually:

```bash
pip install langgraph langchain-openai langchain-tavily tavily-python python-dotenv
```

### 3. Select an LLM provider

In Step 3 of the notebook, set `LLM_PROVIDER` to one of:

| Provider | Model | Notes |
|---|---|---|
| `"groq"` | Llama 3.3 70B | **Recommended** — fast, supports tool calling |
| `"azure"` | GPT-4o-mini (configurable) | Full tool calling support |
| `"huggingface"` | Qwen 2.5 7B | Via HuggingFace Inference Router |
| `"DeepSeek"` | DeepSeek V4 Pro | Limited tool calling support — may cause errors with the agent |

## Notebook Walkthrough

| Step | Cell | Description |
|---|---|---|
| 1 | Install Libraries | Installs `langgraph`, `langchain-openai`, `langchain-tavily`, `tavily-python`, `python-dotenv` |
| 2 | Load Environment Variables | Reads API keys from the `.env` file into `os.environ` and verifies they are loaded |
| 3 | Configure LLM | Sets up the chat model using the selected provider (Groq, HuggingFace, Azure, or DeepSeek) |
| 4 | Define Search Tool | Creates a LangChain `@tool`-decorated function wrapping `TavilySearch` with `max_results=3` |
| 5 | Create Agent | Builds the ReAct agent via `create_agent(model=llm, tools=[search_tool])` |
| 6 | Visualize Graph | Renders the agent's internal state graph showing model and tool nodes |
| 7 | Run with Tool | Invokes the agent with a domain query — agent calls Tavily search and synthesizes results |
| 8 | Run without Tool | Invokes the agent with a simple math query — agent responds directly without using tools |

## Key Concepts

- **ReAct Agent**: An agent pattern where the LLM alternates between **Re**asoning and **Act**ing. It decides at each step whether to call a tool or produce a final answer.
- **LangGraph**: A framework for building stateful, multi-step agent workflows as directed graphs. Each node is a processing step, and edges define the control flow.
- **Tavily Search**: A search API optimized for AI agents, returning concise, relevant web results suitable for LLM consumption.
- **Tool Calling**: The LLM generates structured function-call requests (following the OpenAI tool-calling schema), which the framework routes to the appropriate tool.

## Troubleshooting

| Issue | Cause | Fix |
|---|---|---|
| API key shows `False` | `.env` file not saved or key missing | Save the `.env` file and re-run Step 2 |
| `BadRequestError: 400` | LLM provider doesn't support tool calling | Switch `LLM_PROVIDER` to `"groq"` or `"azure"` |
| `GROQ_API_KEY not found` | Key not in `.env` file | Add `GROQ_API_KEY=gsk_...` to your `.env` file |
| Import errors | Missing packages | Re-run Step 1 (pip install cell) |

## File Structure

```
Demo_3/
├── Demo_03_Building_Reactive_Agent_Workflow_with_LangGraph_and_Tavily.ipynb
├── .env              # API keys (not committed to version control)
└── README.md         # This file
```
