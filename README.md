# LangGraph Multi-Agent Supervisor Workflow

This repository contains an agentic AI workflow built using **LangGraph**. It implements a hierarchical multi-agent system where a central "Supervisor" Large Language Model (LLM) manages and delegates tasks to specialized worker agents (a Researcher and a Writer) to accomplish a user-defined goal.

## Architecture Overview

The system utilizes a State Graph to manage the flow of data and decision-making. 

* **The State (AgentState):** A shared dictionary that travels through the graph, keeping track of the current task, accumulated research notes, the drafted report, and revision feedback.
* **The Supervisor (Brain):** Powered by Groq's `llama-3.3-70b-versatile`. It reviews the current state of the graph and uses structured output (via Pydantic) to route the workflow. It decides whether to send the task to the Researcher, the Writer, or finish the execution.
* **The Researcher:** An agent equipped with the `TavilySearchResults` tool to browse the web and gather context based on the task.
* **The Writer:** An agent that takes the accumulated research notes and drafts the final report.

### Key Features
* **Structured Output Routing:** Uses native structured outputs to force the LLM to return valid routing commands.
* **Human-in-the-Loop (Breakpoints):** The graph uses `MemorySaver` to pause execution right before the "Writer" node acts. This allows a human to review the research gathered and the supervisor's feedback before approving the final draft generation.

## Prerequisites and Setup

Before running the workflow, you will need to set up your environment and obtain the necessary API keys.

1.  **API Keys:**
    * [Groq API Key](https://console.groq.com/) (For the Supervisor and Worker LLMs)
    * [Tavily API Key](https://tavily.com/) (For the Researcher's web search capabilities)

2.  **Environment Variables:**
    Set the following environment variables in your terminal or a `.env` file:
    ```bash
    export GROQ_API_KEY="your_groq_api_key"
    export TAVILY_API_KEY="your_tavily_api_key"
    ```

3.  **Install Dependencies:**
    Install the required Python packages:
    ```bash
    pip install langgraph langchain-groq langchain-community pydantic tavily-python jupyter
    ```

## Running the Notebook

To explore and run the agentic workflow, you will need to execute the Jupyter Notebook. 

1.  Navigate to the directory containing your repository in your terminal.
2.  Run the following command to launch the specific notebook directly:

```bash
jupyter notebook langgraph-supervisor.ipynb
```
(Alternatively, if you are using JupyterLab, you can run jupyter lab langgraph-supervisor.ipynb)

## Execution Flow
When you run the notebook, you will observe the following sequence:

- Initialization: The graph starts with an initial task (e.g., "Impact of LPU architecture on AI inference speeds").

- Routing: The Supervisor evaluates the empty state and routes to the Researcher.

- Researching: The Researcher executes a web search and appends notes to the state.

- Review: The Supervisor reviews the new notes.

- Pause: The system hits the interrupt_before=["writer"] breakpoint. The execution streams pause, allowing you to review the state snapshot.

- Resume: Once you resume execution (passing None to the stream), the Writer takes over and drafts the final content.
