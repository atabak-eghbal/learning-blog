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

---

## Conversation with a ReAct Agent

So far we've just been printing the agent's outputs. It's also useful to verify the agent is responding correctly by printing **both** the user's query and the agent's response.

### Printing user input and agent output

First, define the tools and query. Then set up the agent using a pre-loaded model and tools. Next, invoke the agent and store the response before printing both the query, labeled `"user_input"`, and the agent's response as `"agent_output"`.

```python
# Define tools and query
tools = [rectangle_area]
query = "What is the area of a rectangular apartment with sides 5 and 7 meters?"

# Set up the ReAct agent
app = create_react_agent(model, tools)

# Invoke the agent and store the response
result = app.invoke({"messages": [("user", query)]})

# Print both the user's query and the agent's response
print("user_input:", query)
print("agent_output:", result["messages"][-1].content)
```

When we examine the output, it looks like our agent is working!

### Follow-up questions

To verify that our agent is working, we can ask follow-up questions. While answering, LangChain will update the whole conversation before printing the answer again as a separate output.

When looking at the output, we'll see a new query for a new rectangle, followed by the full conversation listing both the old and new queries with their respective answers:

- **HumanMessage** — our own queries
- **AIMessage** — the agent's answers

If the conversation is up to date, printing the last output will repeat the most recent query and answer, ensuring that the agent works.

### Conversation history

To set up our conversation history, import `HumanMessage` and `AIMessage` from LangChain's core messages module:

```python
from langchain_core.messages import HumanMessage, AIMessage
```

Then set up a variable called `message_history` that will store all messages, seeded with the exchange from the first query so the agent has context when answering the follow-up:

```python
message_history = result["messages"]  # carries over the first conversation
```

Next, define a new query that asks a new question without providing any additional contextual information. Here, we want to know the area of a new rectangle with different dimensions:

```python
new_query = "What about a rectangle with sides 10 and 3 meters?"
```

Invoke the `app` object again, this time passing both the message history and the new query to the agent within a dictionary:

```python
result = app.invoke({"messages": message_history + [("user", new_query)]})
```

Filter out only the relevant messages from the agent's response using a list comprehension that selects both `HumanMessage` and `AIMessage` instances that contain actual content. The `.strip()` call acts as a filter to exclude any messages whose content is empty or whitespace-only:

```python
messages = [
    msg for msg in result["messages"]
    if isinstance(msg, (HumanMessage, AIMessage)) and msg.content.strip()
]
```

Finally, format and print the conversation extracted from `msg.content`, with each message labeled using its proper class name. The `"user_input"` is our new query, while `"agent_output"` prints the full conversation and repeats the agent's most recent output — useful for debugging:

```python
user_input = new_query
agent_output = result["messages"][-1].content

print("user_input:", user_input)
for msg in messages:
    print(f"{msg.__class__.__name__}: {msg.content}")
print("agent_output:", agent_output)
```

### Conversation history output

The output should include:

- The new query labeled `"user_input"`
- The full conversation with labeled `HumanMessage` and `AIMessage` entries
- The most recent query and answer when listing the last message

We have our new query labeled `"user_input"`. Then, we have our agent output listing the full conversation with labeled human and AI messages. Finally, `"agent_output"` repeats the agent's most recent response — the last item in `result["messages"]`. Everything is in good shape!

---

## Mini-quiz: Conversations

1. Why is it useful to print both `"user_input"` and `"agent_output"` rather than just the final response?
2. What is the difference between a `HumanMessage` and an `AIMessage` in LangChain?
3. Why do we use `.strip()` when filtering messages?
4. How does passing `message_history` to `app.invoke()` allow the agent to answer follow-up questions correctly?
