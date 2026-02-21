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

## Adding External Tools to a Chatbot

External API tools augment chatbot agents by enabling access to resources beyond the LLM's training data — such as news sites, databases, and social media platforms. Adding tools transforms a basic chatbot into an agent that can fetch up-to-date, accurate information on demand.

---

## Adding a Wikipedia Tool

LangGraph makes it straightforward to give a chatbot access to Wikipedia using two modules from LangChain:

| Module | Purpose |
|--------|---------|
| `WikipediaAPIWrapper` | Interacts with the Wikipedia API |
| `WikipediaQueryRun` | Wraps the API as a runnable tool for queries |

```python
from langchain_community.utilities import WikipediaAPIWrapper
from langchain_community.tools import WikipediaQueryRun

# Initialise the wrapper, limiting results for relevance
api_wrapper = WikipediaAPIWrapper(top_k_results=1)

# Create the tool and store it in a list (which can hold multiple tools)
wikipedia_tool = WikipediaQueryRun(api_wrapper=api_wrapper)
tools = [wikipedia_tool]
```

- `top_k_results=1` keeps responses focused by returning only the most relevant Wikipedia article.
- Storing the tool in a `tools` list makes it easy to scale to multiple tools later.

---

## Binding Tools to the LLM

Once the tool is defined, bind it to the language model using `.bind_tools()`:

```python
llm_with_tools = llm.bind_tools(tools)

def chatbot(state: State):
    """Generate a response, using Wikipedia when needed."""
    return {"messages": [llm_with_tools.invoke(state["messages"])]}
```

- `llm_with_tools` replaces `llm` in the chatbot function so the model can decide when to call Wikipedia.
- The full conversation stored in `"messages"` is passed to `llm_with_tools`, allowing context-aware tool use.
- For other external API integrations, refer to [LangChain's API documentation](https://python.langchain.com/docs/).

---

## Adding Tool Nodes

To wire the Wikipedia tool into the graph, two additional imports are needed:

| Module | Purpose |
|--------|---------|
| `ToolNode` | Creates a graph node that executes one or more tools |
| `tools_condition` | Conditional routing — routes to tools if needed, otherwise ends |

```python
from langgraph.prebuilt import ToolNode, tools_condition

# Add the chatbot node (as before)
graph_builder.add_node("chatbot", chatbot)

# Define and add the tool node
tool_node = ToolNode(tools=tools)
graph_builder.add_node("tools", tool_node)
```

### Connecting nodes with conditional edges

```python
# Let the chatbot decide whether to call a tool or end
graph_builder.add_conditional_edges("chatbot", tools_condition)

# Route tool responses back to the chatbot
graph_builder.add_edge("tools", "chatbot")

# Connect START to the chatbot
graph_builder.add_edge(START, "chatbot")
```

| Step | Description |
|------|-------------|
| `add_conditional_edges("chatbot", tools_condition)` | Routes to `"tools"` if a tool call is needed; routes to END otherwise |
| `add_edge("tools", "chatbot")` | Sends tool results back to the chatbot for a final response |
| `add_edge(START, "chatbot")` | Begins the workflow at the chatbot node |

---

## Let's practice!

Now it's time to try it yourself! Practice adding external tools to your chatbot and routing through tool nodes using conditional edges.
