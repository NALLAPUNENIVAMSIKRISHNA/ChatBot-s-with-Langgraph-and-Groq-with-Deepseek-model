#  LangGraph ChatBot using Groq LLM

This project is a simple chatbot built using [LangGraph](https://github.com/langchain-ai/langgraph), [LangChain](https://www.langchain.com/), and [Groq's LLM API](https://console.groq.com/). It processes user input in real-time and responds using a powerful large language model (`deepseek-r1-distill-qwen-32b`).

---

## Technologies Used

- **LangGraph** – to build the chatbot logic as a state machine
- **LangChain** – to interface with the LLM
- **Groq API** – to use high-speed LLMs
- **LangSmith** – for tracing and project tracking (optional)

---

##  Setup Instructions

### 1. Install Required Packages

```bash
pip install langgraph langsmith
pip install langchain_groq langchain_community
```

If you're using Google Colab, use `!` before pip commands:

```python
!pip install langgraph langsmith
!pip install langchain_groq langchain_community
```

---

### 2. Set Up API Keys

Get your API keys from:

- Groq → https://console.groq.com/
- LangSmith → https://smith.langchain.com/

In Google Colab:

```python
from google.colab import userdata
groq_api_key = userdata.get('groq_api_key')
langsmith = userdata.get('langsmith_api_key')
```

---

### 3. Set Environment Variables

```python
import os

os.environ["LANGCHAIN_API_KEY"] = langsmith
os.environ["LANGCHAIN_TRACING_V2"] = "true"
os.environ["LANGCHAIN_PROJECT"] = "CourseLanggraph"
```

---

##  Load Groq LLM

```python
from langchain_groq import ChatGroq

llm = ChatGroq(groq_api_key=groq_api_key, model_name="deepseek-r1-distill-qwen-32b")
```

---

##  Build the ChatBot using LangGraph

### Step 1: Define State

```python
from typing import Annotated
from typing_extensions import TypedDict
from langgraph.graph import StateGraph, START, END
from langgraph.graph.message import add_messages

class State(TypedDict):
    messages: list[list, add_messages]
```

### Step 2: Create Graph and Add Node

```python
graph_builder = StateGraph(State)

def chatbot(state: State):
    return {"messages": llm.invoke(state["messages"])}

graph_builder.add_node("chatbot", chatbot)
```

### Step 3: Add Edges and Compile Graph

```python
graph_builder.add_edge(START, "chatbot")
graph_builder.add_edge("chatbot", END)

graph = graph_builder.compile()
```

---

##  Run the ChatBot

```python
while True:
    user_input = input("User : ")
    if user_input.lower() in ["quit", "q"]:
        print("Good Bye")
        break
    for event in graph.stream({'messages': ("user", user_input)}):
        for value in event.values():
            print("Assistant : ", value["messages"].content)
```

---

##  Example Usage

```
User : Hello
Assistant : Hello! How can I assist you today?

User : Tell me about LangGraph
Assistant : LangGraph is a framework for building LLM-powered applications using state machines...
```

---

##  Notes

- You can enhance this chatbot by adding memory, chaining multiple LLM calls, or integrating a UI using Streamlit or Gradio.
- Ideal for learning how to use Groq LLMs with LangGraph in a minimal setup.
