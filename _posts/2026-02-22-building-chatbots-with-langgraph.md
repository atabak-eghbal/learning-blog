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

## Streaming Graph Events

In LangGraph, **streaming events** from a graph lets us process steps in an agent's workflow in real time. Each event represents a single step in the graph — such as generating a response or calling a tool — making it possible to track the chatbot agent's progress and display responses as soon as they are ready. Streaming the graph returns multiple events, including the user's query, so you can see each step as it happens.

---

## Streaming LLM Responses

To stream responses, define a `stream_graph_updates()` function that accepts a `user_input` string and calls the `.stream()` method on the compiled graph:

```python
def stream_graph_updates(user_input: str):
    for event in graph.stream({"messages": [("user", user_input)]}):
        for value in event.values():
            print(value["messages"])

stream_graph_updates('Who is Mary Shelley?')
```

| Step | Description |
|------|-------------|
| `.stream()` | Runs the graph and yields one event per step |
| `event.values()` | Retrieves the chatbot's output from each step |
| `value["messages"]` | Prints the response as soon as it is ready |

Since the chatbot currently has no tools, it uses the LLM's knowledge base directly to answer the test query about Mary Shelley — the celebrated science fiction author of *Frankenstein*. The response is returned inside an `AIMessage` object with a `content` field containing the LLM's detailed answer, and `response_metadata` that includes the name of the model used to generate the response.

---

## LLMs and Hallucinations

Even powerful LLMs can produce **hallucinations** — plausible-sounding but incorrect statements. Always verify claims against original sources. For example, an LLM might incorrectly identify NASA engineer Judith Love Cohen's famous son as "Adam Cohen", when in fact it is Jack Black. Cross-checking with a reliable source will reveal the error.

---

## Generating a LangGraph Diagram

LangGraph can render a visual diagram of the graph's nodes and edges using Mermaid:

```python
from IPython.display import Image, display

try:
    display(Image(graph.get_graph().draw_mermaid_png()))
except Exception:
    print("Additional dependencies are required for Python visualization in this environment.")
```

| Step | Description |
|------|-------------|
| `.get_graph()` | Retrieves the graph's structure |
| `.draw_mermaid_png()` | Renders the structure as a PNG diagram |
| `Image()` / `display()` | Shows the diagram in the notebook |

The `try`/`except` block handles environments where visualization dependencies are not installed.

---

## Let's practice!

Now it's time to try it yourself! Practice streaming responses from your chatbot and generating a diagram of your LangGraph graph.

---

## Mini-quiz

1. What is the difference between a **node** and an **edge** in LangGraph?
2. Why does `State` use `Annotated[list, add_messages]` instead of a plain `list`?
3. What role does `StateGraph` play, and when do you call `.compile()`?
4. Which two nodes are imported directly from LangGraph to mark workflow boundaries?
5. What does each event represent when streaming from a LangGraph graph?
6. Why should you always verify LLM responses against original sources?
7. Which method renders the graph structure as a visual diagram, and which method retrieves its structure?

---

## Transcript

### 1. Adding external tools to a chatbot
**00:00 - 00:05**

Now that you're familiar with basic chatbots, let's try incorporating an

### 2. External tools with LangGraph
**00:05 - 00:23**

external API tool into our chatbot. Tools with API capabilities help augment chatbot agents by enabling access to external resources, such as news sites, databases, social media, and many others.

### 3. Adding a Wikipedia tool
**00:23 - 00:30**

Using LangGraph, let's expand our education chatbot's knowledge by including a Wikipedia API.

### 4. Adding a Wikipedia tool
**00:30 - 01:17**

We'll start with two modules. WikipediaAPIWrapper allows us to interact with the Wikipedia API, while WikipediaQueryRun makes the API a tool for running queries. Next, we'll initialize WikipediaAPIWrapper, setting top_k_results to one to keep responses relevant. Then, we'll create wikipedia_tool with WikipediaQueryRun, passing in the api_wrapper to connect directly to Wikipedia and retrieve detailed information when needed. Finally, we'll store wikipedia_tool in a list called tools, which can hold multiple tools if required.

### 5. Adding a Wikipedia tool
**01:17 - 01:57**

We can bind our tools list to the language model using .bind_tools() and store it in a variable called llm_with_tools. Next, we'll update our original chatbot function to use llm_with_tools instead of llm, enabling responses from the Wikipedia tool when needed, rather than relying on the language model alone. This modified function passes the full conversation stored in "messages" to llm_with_tools, allowing the language model to decide when to pull information from Wikipedia to enhance its responses.

### 6. Other API tools
**01:57 - 02:07**

For more details on how to add different tools with external APIs, be sure to reference LangChain's API documentation.

### 7. Adding tool nodes
**02:07 - 02:30**

Now that we have our Wikipedia tool, the next modules imported for us, called ToolNode and tools_condition, will help us add the tool to our chatbot's graph. As with the basic chatbot, we'll start by adding our chatbot node labeled "chatbot" to the graph_builder using the .add_node() method.

### 8. Adding tool nodes
**02:30 - 02:44**

Then we'll define a tool_node by passing in the wikipedia_tool to the tools argument in LangGraph's ToolNode() class, before adding this node labeled "tools" to the graph_builder.

### 9. Adding tool nodes
**02:44 - 03:05**

Next, before adding an END node explicitly, we'll use the .add_conditional_edges() method with tools_condition to let the chatbot decide if a tool is needed. If it is, the chatbot will call a tool. If not, the chatbot will end without a response.

### 10. Adding tool nodes
**03:05 - 03:25**

For LLM or tool calls that generate a response, we'll connect "tools" back to the "chatbot" using the .add_edge() method, then add a START node which connects to the "chatbot", before finally connecting the "chatbot" to the added END node.

### 11. Let's practice!
**03:25 - 03:30**

That was quite a lot to cover! Let's work in some practice!
