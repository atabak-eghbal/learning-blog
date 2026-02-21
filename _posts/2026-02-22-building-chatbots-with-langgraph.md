---
layout: post
title: "Building Chatbots with LangGraph"
date: 2026-02-22
categories: [agentic-ai]
tags: [langgraph, chatbots, graphs, nodes, edges, agent-state, llm]
---

## Overview

This post covers building custom chatbots using LangGraph — how to model chatbot workflows as graphs, manage conversation state, and wire nodes and edges together into a working application.

---

## Chatbots with LangGraph

Now that we're familiar with LangChain's pre-built agents, we can learn how to create custom chatbots using LangGraph. The key ideas are:

- **Graph and agent states** — manage the chatbot workflow
- **Nodes** — represent individual functions in the workflow
- **Edges** — define the connections between those functions

---

## Graphs and Agent States

In LangGraph, the **graph state** organises the agent's order of tasks — such as tool usage and LLM calls — into a workflow. The graph uses an **agent state** to track the agent's progress as text to help determine when a task is complete.

| Concept | Description |
|---------|-------------|
| **Graph state** | Ordered workflow of tasks (tool calls, LLM calls, …) |
| **Agent state** | Text-based tracker of the agent's current progress |

---

## Building an Agent with LangGraph

A simple education chatbot that uses an LLM to answer questions only needs three nodes and two edges:

| Element | Role |
|---------|------|
| **START** | Beginning of the workflow |
| **Chatbot** | Node that calls the LLM to generate a response |
| **END** | End of the workflow |

The two edges connect **START → Chatbot** and **Chatbot → END**.

---

## Nodes and Edges

In LangGraph:

- **Nodes** represent functions, such as generating a response or calling a tool.
- **Edges** define the connections between nodes and determine the flow of execution.

The `START` and `END` nodes mark the boundaries of the workflow and are imported directly from LangGraph.

---

## Building Graph and Agent States

The following modules are required:

```python
from typing import Annotated
from typing_extensions import TypedDict
from langgraph.graph import StateGraph, START, END
from langgraph.graph.message import add_messages
from langchain_openai import ChatOpenAI
```

| Module | Purpose |
|--------|---------|
| `Annotated`, `TypedDict` | Organise and type the chatbot's text data |
| `StateGraph` | Sets up the chatbot's workflow graph |
| `START`, `END` | Define the beginning and end of the workflow |
| `add_messages` | Adds text messages to the state with metadata |
| `ChatOpenAI` | Provides the LLM |

### Defining the LLM and agent state

```python
# Set up the LLM
llm = ChatOpenAI()

# Define the agent state
class State(TypedDict):
    # Stores all text interactions; add_messages appends new messages with metadata
    messages: Annotated[list, add_messages]

# Initialise the graph builder with the State structure
graph_builder = StateGraph(State)
```

- `State` uses `TypedDict` to structure messages as a typed dictionary.
- `Annotated[list, add_messages]` ensures new messages are appended to the list with metadata rather than replacing it.
- `StateGraph(State)` creates the graph builder that will organise nodes and edges.

---

## Adding Nodes and Edges

### Define and add the chatbot node

```python
def chatbot(state: State):
    """Generate a response using the conversation history stored in state."""
    return {"messages": [llm.invoke(state["messages"])]}

graph_builder.add_node("chatbot", chatbot)
```

The `chatbot` function accepts the `State` class and calls `llm.invoke()` with the `"messages"` content to generate a response based on the conversation so far.

### Connect nodes with edges and compile

```python
# Start → chatbot
graph_builder.add_edge(START, "chatbot")

# chatbot → End
graph_builder.add_edge("chatbot", END)

# Compile the graph into a runnable application
app = graph_builder.compile()
```

- `add_edge(START, "chatbot")` begins the conversation at the chatbot node.
- `add_edge("chatbot", END)` finishes the conversation after the chatbot responds.
- `.compile()` turns the graph builder into a runnable application.

### Running the chatbot

```python
result = app.invoke({"messages": [("user", "What is machine learning?")]})
print(result["messages"][-1].content)
```

---

## Mini-quiz

1. What is the difference between a **node** and an **edge** in LangGraph?
2. Why does `State` use `Annotated[list, add_messages]` instead of a plain `list`?
3. What role does `StateGraph` play, and when do you call `.compile()`?
4. Which two nodes are imported directly from LangGraph to mark workflow boundaries?
