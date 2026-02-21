---
layout: post
title: "Agents in LangChain"
date: 2026-02-21
categories: [agentic-ai]
tags: [langchain, langgraph, react-agent, tools, llm]
---

## Overview

This post covers designing agentic systems with LangChain — how to build autonomous AI systems that use tools to complete tasks.

---

## Agents and Tools

| Concept | Description |
|---------|-------------|
| **Agents** | Autonomous systems that make decisions and take actions |
| **Tools** | Functions agents use to perform specific tasks |

### Example tool use cases
- Data query
- Research reports
- Data analysis

---

## Basic Concepts

Building AI agents with LangChain relies on a few core building blocks:

- **LLMs** (e.g., ChatGPT) — the reasoning engine
- **Prompts** — instructions that guide the LLM
- **Tools** — functions the agent can call
- **API** — interface to external services
- **LangChain** — the framework that wires everything together

---

## What You Can Build

With LangChain agents you can tackle problems like:

- Solving **math problems** programmatically
- Running **Wikipedia searches** automatically
- Switching dynamically between **tools and LLMs** depending on the task

---

## Why Agents Beat Plain LLMs for Math and Code

LLMs can make mistakes on seemingly simple calculations (see [this OpenAI community thread](https://community.openai.com/t/chatgpt-simple-math-calculation-mistake/62780)). Delegating computation to a tool (e.g., a Python interpreter) removes that error class entirely.

### Order of Math Operations (PEMDAS)

1. **P**arentheses
2. **E**xponents
3. **M**ultiplication / **D**ivision (left to right)
4. **A**ddition / **S**ubtraction (left to right)

Rather than trusting the LLM to apply these rules, an agent can call a math tool and get the correct answer every time.

---

## Expanding Agents with LangGraph

LangGraph lets you model agent workflows as **graphs**, giving you fine-grained control over execution flow.

### Graph primitives

| Primitive | Role |
|-----------|------|
| **Nodes** | Individual steps, e.g. *Query the Database* or *Return the Document* |
| **Edges** | Rules that connect nodes and determine transitions |

---

## Creating a ReAct Agent

The **ReAct** (Reason + Act) pattern interleaves reasoning traces with tool calls. LangGraph ships a ready-made helper for it.

```python
# Module imports
from langchain_core.tools import tool
from langchain_openai import ChatOpenAI
from langgraph.prebuilt import create_react_agent
import math

# LLM setup — reads OPENAI_API_KEY from the environment automatically
model = ChatOpenAI()

# Define a custom tool
@tool
def square_root(x: float) -> float:
    """Return the square root of x."""
    return math.sqrt(x)

# Build the agent
agent = create_react_agent(model, tools=[square_root])

# Run the agent
result = agent.invoke({"messages": [("user", "What is the square root of 144?")]})
print(result["messages"][-1].content)
```

> **Tip:** Never hard-code your API key. Use an environment variable (`OPENAI_API_KEY`) and pass it via `os.environ` or a secrets manager.

---

## Mini-quiz

1. What is the difference between an **agent** and a **tool**?
2. Why is it better to call a math tool than to ask the LLM to do arithmetic directly?
3. In LangGraph, what determines which node runs next?
4. What does the "Re" and "Act" in *ReAct* stand for?

---

## Building Custom Tools

### Use Case: Calculating Square Footage

Real estate agency staff may need to calculate the square footage of rectangular one-bed apartments. We can build a math tool using the lengths of the sides of the apartment, supplied via casual, natural language.

By default, LangChain accepts such natural language queries as strings. Internally, it uses the LLM to extract the necessary input from the query — for example, LangChain might extract the two numbers `"5"` and `"7"` as string inputs representing rectangle lengths before performing calculations.

### Creating a math tool

```python
from langchain_core.tools import tool
from langchain_openai import ChatOpenAI
from langgraph.prebuilt import create_react_agent

# Use the @tool decorator so LangChain recognises this function as a tool
@tool
def rectangle_area(measurements: str) -> float:
    """Calculate the area of a rectangle given the lengths of two sides, a and b."""
    a, b = measurements.split(",")
    return float(a.strip()) * float(b.strip())
```

The `@tool` decorator tells LangChain to treat `rectangle_area` as an agent tool. The function:
- Accepts a **string** extracted from the natural language query
- Splits the string into the two side lengths `a` and `b`
- Uses `.strip()` to remove any surrounding whitespace before converting each to a `float`
- Returns the computed area

### Tools and query setup

```python
# LLM setup
model = ChatOpenAI()

# Make the tool available to the agent
tools = [rectangle_area]

# Natural language query — could come from a real estate chatbot
query = "What is the area of a rectangular apartment with sides 5 and 7 meters?"

# Build the ReAct agent
app = create_react_agent(model, tools)

# Invoke the agent and print its response
result = app.invoke({"messages": [("user", query)]})
print(result["messages"][-1].content)
```

Although only one tool is listed here, you can add as many tools as your workflow requires.

### Pre-built and custom tools

LangChain also ships an extensive library of **pre-built tools** for common problems:

| Category | Examples |
|----------|---------|
| **Database** | SQL query, vector store retrieval |
| **Web** | Web scraping, search engines |
| **Media** | Image generation |

Refer to [LangChain's API guide](https://python.langchain.com/docs/integrations/tools/) for available pre-built tools, and to the [tool decorator guide](https://python.langchain.com/docs/how_to/custom_tools/) for building additional custom tools.
