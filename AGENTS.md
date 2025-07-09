```markdown
# AGENTS.md: Instructions for AI Agents Working with the Biomni Repository

Welcome, AI Agent! This document provides guidance for working effectively with the Biomni codebase. Your goal is to assist users by completing coding tasks, analyzing data, and contributing to the Biomni ecosystem.

## 1. Core Mission & Understanding

*   **Biomni's Purpose:** Biomni is a general-purpose biomedical AI agent designed to autonomously execute research tasks. It integrates Large Language Model (LLM) reasoning with retrieval-augmented planning and code-based execution.
*   **Key Components:**
    *   **Agents (`biomni/agent/`):** Different agent implementations (e.g., `A1.py` is the most current). They manage interaction flow, tool usage, and LLM communication.
    *   **Tools (`biomni/tool/`):** Python functions that perform specific biomedical tasks (e.g., sequence analysis, database queries, data visualization). Each tool typically has a corresponding description in `biomni/tool/tool_description/`.
    *   **Tasks (`biomni/task/`):** Benchmark tasks used to evaluate agent performance. They define a problem, provide data, and specify evaluation criteria.
    *   **Environment (`biomni_env/`):** Contains scripts and configuration for setting up the extensive Conda and R environment required by Biomni.
    *   **Utilities (`biomni/utils.py`):** Helper functions for LLM interaction, schema generation, tool creation, code execution, etc.
    *   **Data Lake:** Biomni utilizes a local data lake (typically in `./data/biomni_data/data_lake/`) containing various biomedical datasets.

## 2. Getting Started & Environment

*   **Environment Complexity:** The Biomni environment is large and includes many specific Python packages, R packages, and command-line tools. Refer to `biomni_env/README.md`, `biomni_env/environment.yml`, `biomni_env/r_packages.yml`, and `biomni_env/install_cli_tools.sh`.
*   **API Keys:** Biomni requires API keys for LLMs (Anthropic, OpenAI). These are typically set as environment variables (`ANTHROPIC_API_KEY`, `OPENAI_API_KEY`). Ensure these are configured in your execution environment if you need to run the agent.
*   **Data Path:** The primary data path is usually `./data`, with actual datasets often under `./data/biomni_data/`. The `A1` agent can download initial data if it's missing.

## 3. Working with Code

### 3.1. General Principles

*   **Read `README.md` and `CONTRIBUTION.md`:** These files provide crucial project context and contribution guidelines.
*   **Modularity:** Strive to maintain and enhance the modular design. Tools should be self-contained units of functionality.
*   **Clarity and Readability:** Write clear, well-documented code. Biomedical research often involves complex logic; make it as understandable as possible.
*   **Error Handling:** Implement robust error handling, especially in tools that call external APIs, CLI tools, or perform complex data manipulations. Return informative error messages.
*   **Logging:** Tools often return a log of their operations. Maintain this pattern as it's helpful for users to understand what the agent did. The `A1` agent also maintains a log of its execution.

### 3.2. Agent Development (`biomni/agent/`)

*   **`A1.py` as Primary:** The `A1.py` agent appears to be the most feature-rich and current. Focus development efforts here or use it as a reference.
*   **Agent Similarities:** Note the significant code overlap between different agent classes (`A1`, `biomni_agent`, `qa_agent`, `react`). Consider refactoring common functionalities into a base class if making substantial changes to agent logic.
*   **System Prompts:** Pay close attention to how system prompts are generated (`_generate_system_prompt` in `A1.py`). These are critical for guiding the LLM.
*   **Tool Execution:**
    *   `A1` uses custom XML-like tags (`<execute>`, `<solution>`) to manage code execution and responses.
    *   Other agents (`qa_agent`, `react`) use Langchain's standard tool calling.
*   **Custom Resources:** `A1.py` supports dynamic addition/removal of custom tools, data, and software. If your task involves this, ensure you update the agent's state and system prompt correctly.

### 3.3. Tool Development (`biomni/tool/`)

*   **Tool Implementation:** Place new tool functions in the appropriate subject-area Python file (e.g., `genomics.py`, `pharmacology.py`).
*   **Tool Description:**
    *   Create a corresponding schema entry in `biomni/tool/tool_description/`.
    *   Use `biomni.utils.function_to_api_schema` to help generate these schemas, as described in `CONTRIBUTION.md`. The LLM used by this function will help create the JSON-like dictionary structure.
    *   Ensure schemas accurately describe parameters (name, type, description, default). This is vital for the agent to use the tool correctly.
*   **Wrapping CLI Tools:** When wrapping command-line tools (e.g., MACS2, HOMER):
    *   Use `subprocess.run()` for execution.
    *   Capture `stdout` and `stderr`.
    *   Return a comprehensive log string detailing the command run, key outputs, and paths to result files.
    *   Ensure necessary CLI tools are part of the environment setup (`biomni_env/install_cli_tools.sh`).
*   **Data Access:** Tools may need to access the data lake. Use the provided path (often configurable via a parameter like `DATA_LAKE` or `data_dir`).
*   **Idempotency:** Where possible, design tools to be idempotent or to handle re-execution gracefully.

### 3.4. Task Development (`biomni/task/`)

*   Tasks inherit from `biomni.task.base_task.base_task`.
*   They typically involve loading data, defining a prompt, providing an evaluation mechanism (`evaluate` method), and specifying an output Pydantic class (`output_class` method).

### 3.5. Utilities (`biomni/utils.py`)

*   Familiarize yourself with the utilities provided, especially for:
    *   Code execution: `run_r_code`, `run_bash_script`, `run_with_timeout`.
    *   Schema generation: `function_to_api_schema`.
    *   Tool conversion: `api_schema_to_langchain_tool`.
*   If you create a new general-purpose utility, consider adding it here.

## 4. Testing

*   **Local Testing:** Test your changes thoroughly in a properly configured Biomni environment.
*   **Tool Testing:** When adding a new tool, create a test prompt that utilizes your tool and verify the agent works correctly with it. (As per `CONTRIBUTION.md`).
*   **Task-Based Evaluation:** If your changes affect agent behavior, consider evaluating performance on relevant benchmark tasks in `biomni/task/`.

## 5. Dependencies and Environment

*   **Managing Dependencies:**
    *   If adding a new Python dependency, add it to `biomni_env/environment.yml` with a specific version if possible.
    *   If adding an R package, add it to `biomni_env/r_packages.yml` and potentially update `biomni_env/install_r_packages.R`.
    *   If adding a CLI tool, update `biomni_env/install_cli_tools.sh`.
*   **Renovate/Dependabot:** This repository should ideally use Dependabot (or Renovate) for automated dependency updates. Be mindful of PRs generated by these tools.

## 6. Code Analysis & Refactoring Insights (For Your Information)

*   **Agent Consolidation:** There's an opportunity to consolidate common logic from the various agent classes in `biomni/agent/` into a shared base class. `A1.py` is the most advanced.
*   **Configuration Management:** Centralized configuration for paths (like `DATA_LAKE`) and LLM settings could be beneficial.
*   **Error Handling Standardization:** Efforts to standardize error reporting from tools would be valuable.

## 7. Reporting and Output

*   **Clarity:** When reporting results or analysis, be clear, concise, and structured.
*   **File Paths:** When tools generate files, always report the full and correct paths to these files.
*   **Log Information:** Preserve and present the log information generated by tools and the agent, as this provides valuable context.

By following these guidelines, you can contribute effectively to the Biomni project. If anything is unclear, or if you identify areas for improvement in these instructions, please flag it.
```
