# Agent Concepts Leveraging Biomni's Core Architecture

This document outlines several agent concepts designed to utilize the core strengths of the Biomni agent architecture (particularly `A1.py`) with low to moderate implementation effort. These concepts focus on domains outside of Biomni's primary molecular biology/genomics focus, emphasizing tasks that involve orchestrating tools for information retrieval, API interaction, simple data processing, and leveraging LLM reasoning.

## Core Biomni Strengths Leveraged:

*   **LLM-Driven Planning and Reasoning:** Agents can understand complex prompts, break them into steps, and reason about tool usage.
*   **Flexible Tool System:** Easy registration and use of new tools (Python functions, R scripts, Bash commands) via schema definitions.
*   **Multi-Language Code Execution:** Ability to run Python, R, and Bash scripts/commands.
*   **Customizable System Prompts:** Agent behavior can be tailored by defining available tools and resources.
*   **Information Synthesis:** LLMs can summarize, compare, and draw insights from data gathered by tools.

---

## Agent Concept 1: "LitReview Assistant"

*   **Purpose and Scope:**
    *   To assist academic researchers in conducting targeted literature reviews, summarizing findings, and identifying key papers or trends related to specific sub-topics. It focuses on publicly available academic databases and web resources.
*   **Key User Interactions/Prompts:**
    *   "Find 5 recent review articles on the role of microRNA-21 in glioblastoma progression and summarize their key common findings."
    *   "Identify the top 3 most cited researchers publishing on CRISPR-Cas9 applications in plant genomics since 2020."
    *   "What are the current open questions or controversies regarding the use of AI in medical diagnosis, based on papers from the last 2 years?"
    *   "Generate an annotated bibliography of papers discussing [specific protein] interaction with [specific pathway] in the context of [disease]."
*   **Potential Tools:**
    1.  `search_pubmed(query: str, max_results: int = 10) -> list[dict]`: Wraps NCBI Entrez API for PubMed searches.
    2.  `search_google_scholar(query: str, max_results: int = 5) -> list[dict]`: Uses `view_text_website` for Google Scholar (scraping).
    3.  `fetch_full_text_summary(url: str) -> str`: Uses `view_text_website` for accessible full text and LLM for summarization.
    4.  `get_crossref_metadata(doi: str) -> dict`: Fetches metadata from CrossRef API.
    5.  `extract_entities_from_text(text: str, entity_types: list[str]) -> dict`: Uses LLM to extract specified entities.
    6.  `text_summarizer(text: str, length: str = "medium") -> str`: LLM-based text summarization.
    7.  `manage_bibliography(action: str, entry: dict = None, collection_name: str = "default") -> list[dict]`: Simple Python tool for managing bibliography entries (in-memory or temp file).
*   **Effort Level:**
    *   **Tool Development:** Low to Moderate.
    *   **Agent Configuration:** Low.
*   **Why Biomni's Architecture is a Good Fit:**
    *   LLM planning for multi-step review tasks.
    *   Orchestration of various search and metadata tools.
    *   LLM-driven synthesis of information from multiple sources.

---

## Agent Concept 2: "TechDoc Helper"

*   **Purpose and Scope:**
    *   To assist software developers and technical writers with common documentation tasks, such as generating drafts, checking consistency, and formatting.
*   **Key User Interactions/Prompts:**
    *   "Generate a draft API documentation for this Python file: `[path_to_file.py]`."
    *   "Check all Markdown files in `./docs` for broken internal links."
    *   "Convert `README.md` to HTML using Pandoc."
    *   "List all TODO comments in the `src/` directory."
    *   "Summarize the main changes from commit logs between tag `v1.0` and `v1.1` for the release notes."
*   **Potential Tools:**
    1.  `extract_code_structure(filepath: str, language: str = "python") -> list[dict]`: Uses Python's `inspect` or language-specific CLIs to get function/class signatures and docstrings.
    2.  `llm_draft_documentation(code_info: dict, style: str = "google") -> str`: Uses LLM to draft fuller documentation from extracted code structure.
    3.  `find_broken_markdown_links(directory_path: str) -> list[dict]`: Python script to parse Markdown files and validate local links.
    4.  `convert_document_format(input_file: str, output_format: str, output_file: str = None) -> str`: Wrapper for Pandoc CLI.
    5.  `search_code_comments(directory: str, pattern: str = "TODO|FIXME") -> list[str]`: Uses Bash `grep`.
    6.  `get_git_log_summary(repo_path: str, commit_range: str) -> str`: Uses `git log` and LLM for summarization.
    7.  `spell_check_document(filepath: str, language: str = "en_US") -> list[dict]`: Wrapper for a CLI spell checker (e.g., `aspell`).
*   **Effort Level:**
    *   **Tool Development:** Low to Moderate.
    *   **Agent Configuration:** Low.
*   **Why Biomni's Architecture is a Good Fit:**
    *   Multi-language execution for CLIs (Pandoc, Git, grep).
    *   File system operations for reading code and documentation files.
    *   LLM for content generation (drafting docs) and summarization (commit logs).

---

## Agent Concept 3: "EcoFootprint Advisor"

*   **Purpose and Scope:**
    *   To provide users with information and suggestions related to environmental sustainability, focusing on calculating and reducing personal/household carbon footprints using publicly available data and APIs.
*   **Key User Interactions/Prompts:**
    *   "What's the estimated carbon footprint of a round-trip flight from New York (JFK) to London (LHR) in economy class?"
    *   "Suggest 3 ways I can reduce my home energy consumption if I live in a [type of house] in [city/region] and use [heating_type]."
    *   "Find local recycling guidelines for [item] in [city, state]."
    *   "Compare the typical CO2 emissions per passenger mile for train vs. short-haul flight."
*   **Potential Tools:**
    1.  `calculate_flight_emissions(origin_airport_code: str, destination_airport_code: str, class: str = "economy") -> dict`: Wrapper for a public flight carbon calculator API or web scraper.
    2.  `get_transport_emission_factors(transport_mode: str) -> dict`: Uses `view_text_website` to fetch emission factors from sources like EPA, EEA.
    3.  `find_local_recycling_info(item: str, city: str, state: str) -> str`: Uses `view_text_website` with search engine queries for local government/waste management guidelines.
    4.  `llm_generate_energy_saving_tips(housing_type: str, climate_info: str, heating_type: str, habits: str = None) -> list[str]`: LLM generates tailored suggestions based on general principles (possibly fetched once).
    5.  `search_sustainable_resources_locally(resource_type: str, location: str) -> list[dict]`: Uses `view_text_website` with mapping services or specialized directories (e.g., for farmers' markets, EV charging).
*   **Effort Level:**
    *   **Tool Development:** Low to Moderate (heavy reliance on web queries/APIs; robust parsing is key).
    *   **Agent Configuration:** Low.
*   **Why Biomni's Architecture is a Good Fit:**
    *   `view_text_website` is crucial for information retrieval from diverse online sources.
    *   API integration for structured data (e.g., flight emissions).
    *   LLM can tailor general advice to specific user contexts.
    *   Planning needed for multi-step queries (e.g., "find recycling rules, then find nearest drop-off").

---
## Agent Concept 4: "Historical Event Explorer"

*   **Purpose and Scope:**
    *   To assist users (students, researchers, hobbyists) in exploring historical events, figures, and periods by gathering, cross-referencing, and synthesizing information from public web sources and structured databases.
*   **Key User Interactions/Prompts:**
    *   "Tell me about the key events leading up to the French Revolution, include a timeline."
    *   "Compare the accounts of the Battle of Hastings from three different online sources."
    *   "Who were the main figures involved in the Italian Renaissance, and what were their major contributions?"
    *   "Generate a brief report on the impact of the Silk Road on cultural exchange."
*   **Potential Tools (Low/Moderate Effort Focus):**
    1.  `search_web_for_historical_info(query: str, num_sources: int = 3) -> list[dict]`: (Wrapper for `view_text_website`) Performs web searches (e.g., Wikipedia, Britannica, reputable history sites) and returns URLs and snippets for top results.
    2.  `fetch_web_content_as_text(url: str) -> str`: (Uses `view_text_website`) Fetches textual content from a given URL.
    3.  `llm_extract_historical_data(text: str, data_points: list[str] = ["key_events", "dates", "figures", "locations"]) -> dict`: LLM parses text to extract structured historical information.
    4.  `llm_synthesize_historical_narrative(information_list: list[dict], topic: str, style: str = "summary") -> str`: LLM combines information from multiple sources into a coherent narrative or summary.
    5.  `python_create_timeline(events_data: list[dict]) -> list[str]`: Python script to sort events chronologically and format them as a simple text-based timeline.
    6.  `query_wikidata_sparql(sparql_query: str) -> list[dict]`: (Moderate effort) Executes a SPARQL query against Wikidata for structured historical facts.
*   **Effort Level:**
    *   **Tool Development:** Low (for web/LLM tools) to Moderate (for SPARQL tool).
    *   **Agent Configuration:** Low.
*   **Why Biomni's Architecture is a Good Fit:**
    *   Excellent for orchestrating web searches, content extraction, and LLM-based synthesis.
    *   The planning capability can manage multi-source research and comparison tasks.
    *   Ability to call Python for simple data organization (like timeline sorting).

---

## Agent Concept 5: "Board Game Companion"

*   **Purpose and Scope:**
    *   To assist board game players by clarifying rules, looking up specific game terms or components, and offering basic strategic hints based on publicly available rulebooks and community discussions (e.g., BoardGameGeek).
*   **Key User Interactions/Prompts:**
    *   "In 'Wingspan', how does the 'repeat a brown power' action work?"
    *   "What are the starting resources for each faction in 'Scythe'?"
    *   "Find beginner tips for playing 'Terraforming Mars'."
    *   "Summarize the main ways to score points in 'Catan'."
*   **Potential Tools (Low/Moderate Effort Focus):**
    1.  `search_boardgamegeek(game_name: str, search_type: str = "rules" | "forum_tips") -> list[dict]`: (Uses `view_text_website`) Searches BGG for rulebook links or relevant forum threads and returns URLs/summaries.
    2.  `fetch_web_content_as_text(url: str) -> str`: (Uses `view_text_website`) Fetches rulebook text or forum content.
    3.  `llm_answer_rule_question(rule_text_segment: str, question: str) -> str`: LLM interprets a segment of rules to answer a specific user question.
    4.  `llm_summarize_game_strategy_from_text(forum_text_or_guide: str, game_name: str) -> str`: LLM extracts and summarizes strategic advice from provided text.
    5.  `python_extract_text_from_pdf_url(pdf_url: str) -> str`: (Requires `PyPDF2` or similar library) Python tool to download and extract text from a PDF rulebook.
*   **Effort Level:**
    *   **Tool Development:** Low to Moderate (scraping BGG and PDF parsing are the main complexities).
    *   **Agent Configuration:** Low.
*   **Why Biomni's Architecture is a Good Fit:**
    *   Good for managing workflows like "search for game X, find rulebook, extract text, find section Y, answer question Z."
    *   LLM is key for natural language understanding of rules and generating human-readable explanations/tips.

---

## Agent Concept 6: "Public Health Data Reporter"

*   **Purpose and Scope:**
    *   To fetch, process, and present key indicators and trends from public health data sources (e.g., WHO, CDC APIs) in a summarized, textual format. Focus is on data retrieval and basic interpretation, not complex statistical analysis or direct visualization generation.
*   **Key User Interactions/Prompts:**
    *   "What are the latest WHO figures for malaria incidence in Sub-Saharan Africa?"
    *   "Show the trend of adult obesity rates in the USA over the last 5 years, according to CDC data."
    *   "Compare the reported measles vaccination coverage for three European countries."
*   **Potential Tools (Low/Moderate Effort Focus):**
    1.  `query_who_gho_api(indicator_code: str, region: str = None, country_code: str = None, year: str = None) -> list[dict]`: Wrapper for WHO Global Health Observatory (GHO) OData API.
    2.  `query_cdc_data_api(dataset_id: str, filters: dict = None) -> list[dict]`: Wrapper for CDC SODA API or other specific CDC data APIs.
    3.  `python_process_health_data(data: list[dict], calculation: str = "trend" | "latest_value" | "comparison", relevant_keys: list[str]) -> str`: Python (Pandas) tool for simple data extraction, calculating trends (e.g., percentage change), or formatting comparisons.
    4.  `llm_summarize_health_data_findings(processed_data_summary: str, original_query: str) -> str`: LLM to present the findings from the processed data in a natural language report.
    5.  `search_healthdata_gov(query: str) -> list[dict]`: (Uses `view_text_website`) Searches HealthData.gov for relevant datasets or reports and returns links/summaries.
*   **Effort Level:**
    *   **Tool Development:** Moderate (API wrappers for potentially complex health data APIs require careful parameter mapping and response parsing).
    *   **Agent Configuration:** Low.
*   **Why Biomni's Architecture is a Good Fit:**
    *   Suitable for handling API calls and processing structured (JSON/CSV) data.
    *   Python tools for data manipulation are well-supported.
    *   LLM can translate structured data and simple analyses into understandable reports.

---

## Agent Concept 7: "OSS Contributor Scout"

*   **Purpose and Scope:**
    *   To help aspiring contributors find suitable open-source projects and "good first issues" by interacting with GitHub API and parsing project information.
*   **Key User Interactions/Prompts:**
    *   "Find Python projects on GitHub with 'good first issues' related to data science."
    *   "Show me open issues labeled 'help wanted' in the `[repository_owner]/[repository_name]` repository that don't require deep knowledge of the existing codebase."
    *   "What are the main technologies used in the `[repository_url]` project, and what are its contribution guidelines?"
*   **Potential Tools (Low/Moderate Effort Focus):**
    1.  `search_github_repositories(query: str, language: str = None, sort: str = "stars", max_results: int = 5) -> list[dict]`: Wrapper for GitHub API's repository search endpoint.
    2.  `get_github_repository_issues(repo_owner: str, repo_name: str, labels: list[str] = None, state: str = "open") -> list[dict]`: Wrapper for GitHub API's issues endpoint.
    3.  `get_github_file_content(repo_owner: str, repo_name: str, file_path: str = "CONTRIBUTING.md") -> str`: Fetches content of a specific file (like `CONTRIBUTING.md` or `README.md`) from GitHub API.
    4.  `llm_analyze_issue_suitability(issue_title: str, issue_body: str, issue_labels: list[str], desired_keywords: list[str]=None) -> dict`: LLM to assess if an issue seems beginner-friendly or matches keywords.
    5.  `llm_summarize_repo_info(readme_text: str, contributing_text: str = None, languages_data: dict = None) -> str`: LLM to provide a concise summary of a project.
*   **Effort Level:**
    *   **Tool Development:** Moderate (GitHub API wrapper needs to handle authentication and various endpoints).
    *   **Agent Configuration:** Low.
*   **Why Biomni's Architecture is a Good Fit:**
    *   Excellent for API-centric workflows.
    *   LLM can help parse and interpret semi-structured text from issue descriptions or `CONTRIBUTING.md` files.
    *   Planning can manage multi-step searches (find repos, then find issues within them).

---

## Agent Concept 8: "Personal Habit Analyst"

*   **Purpose and Scope:**
    *   To help users analyze self-tracked data (e.g., habits, mood, productivity) provided in simple formats (like CSV). The agent performs basic data aggregation and uses an LLM to identify simple patterns and offer reflections, not clinical advice.
*   **Key User Interactions/Prompts:**
    *   "Analyze my habit tracking CSV `[path_to_file.csv]`. Which habits have the best completion rate this month?"
    *   "I've been tracking my mood (1-5 scale) and sleep hours in this file: `[data.csv]`. Are there any obvious correlations?"
    *   "Based on my goal of 'writing 500 words daily' and this progress log `[log.txt]`, give me some encouragement and identify days I struggled."
*   **Potential Tools (Low/Moderate Effort Focus):**
    1.  `python_load_and_describe_csv(file_path_or_csv_string: str) -> dict`: Uses Pandas to load CSV, show column names, and provide basic stats (mean, median for numerical columns if specified).
    2.  `python_calculate_completion_rates(data_frame_pickle: bytes, habit_column: str, date_column: str, time_window: str = "monthly") -> dict`: Python/Pandas to calculate completion rates or frequencies. (Using pickled DataFrame to pass data between Python tools can be an option).
    3.  `python_simple_correlation(data_frame_pickle: bytes, column1: str, column2: str) -> dict`: Python/Pandas to calculate Pearson correlation between two columns.
    4.  `llm_interpret_data_patterns(data_summary: dict, user_notes_or_goals: str = None) -> str`: LLM to describe observed patterns, completion rates, or correlations in natural language.
    5.  `llm_generate_reflective_prompts(analysis_summary: str, user_goal: str = None) -> list[str]`: LLM to offer motivational messages or questions for reflection based on the data.
*   **Effort Level:**
    *   **Tool Development:** Low to Moderate (Pandas scripts are relatively straightforward).
    *   **Agent Configuration:** Low.
*   **Why Biomni's Architecture is a Good Fit:**
    *   Can execute Python/Pandas scripts for data processing.
    *   LLM is well-suited for interpreting numerical results in a qualitative way and generating personalized textual feedback.
    *   The system can manage a sequence of "load data -> process data -> interpret results."

---
This markdown file now contains all 8 agent concepts.
