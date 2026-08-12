# Iterative Workflows

An interactive Python workflow that generates, reviews, and iteratively improves LinkedIn posts using LangGraph, Groq LLMs, and Tavily web search.

## Overview

This project builds a lightweight agent loop:

- A writer model drafts a LinkedIn post for a user-provided topic
- The system can call a web search tool when fresh context is needed
- A reviewer model evaluates the draft against publishing criteria
- If the draft is rejected, the workflow loops back and improves it
- The process stops once the post is approved or the maximum number of attempts is reached

The application is implemented as a stateful graph in `iterative.py` and runs from the command line.

## What it does

The workflow follows this sequence:

1. Accept a topic from the user
2. Generate an initial draft with a LinkedIn-focused writing prompt
3. Decide whether the writer needs to use a web search tool
4. Extract the final draft text from the model output
5. Send the draft to a strict reviewer
6. Approve or reject the post
7. Rewrite the draft if rejected, up to a maximum of 3 attempts

## Tech stack

- Python
- LangGraph
- LangChain
- Groq (`ChatGroq`)
- Tavily Search (`TavilySearch`)
- Python-dotenv
- Pydantic

## Project structure

```text
.
├── .env                       # Local environment variables (not committed)
├── .gitignore                 # Ignores local env and generated files
├── iterative.py               # Main workflow implementation
├── requirements.txt           # Python dependencies
├── ienv/                      # Project virtual environment
└── README.md                  # Project documentation
```

## Setup

### 1) Use the existing project environment

This workspace already includes a virtual environment under `ienv/`, so the recommended path is to use it instead of creating another one.

### 2) Install dependencies

From the project root:

```bash
./ienv/Scripts/python -m pip install -r requirements.txt
```

On Windows PowerShell, this is typically:

```powershell
.\ienv\Scripts\python -m pip install -r requirements.txt
```

### 3) Configure environment variables

The app loads values from a `.env` file at runtime.

Create a `.env` file in the project root with:

```env
GROQ_API_KEY="your_groq_api_key"
TAVILY_API_KEY="your_tavily_api_key"
```

These keys are required for:

- Groq model inference
- Tavily web search

## Running the app

From the project root:

```bash
./ienv/Scripts/python iterative.py
```

Or in PowerShell:

```powershell
.\ienv\Scripts\python .\iterative.py
```

Then type a topic when prompted, for example:

```text
What topic do you want a LinkedIn post about?
> AI agents for startup founders
```

The script will print the generated post, the reviewer verdict, and the final approved version.

## Key implementation notes

### State graph

The app uses `StateGraph` and defines a workflow state with:

- `topic`
- `messages`
- `draft`
- `review_feedback`
- `is_approved`
- `attempt`

### Writer node

The writer prompt instructs the model to create a polished LinkedIn post with:

- a strong opening hook
- a single clear takeaway
- short, scannable paragraphs
- roughly 150–200 words
- a closing question or CTA
- no hashtags

### Reviewer node

The reviewer model is stricter and enforces publish-readiness rules. It returns either:

- `APPROVED`
- `REJECTED`

and a short feedback paragraph explaining the reason.

### Loop behavior

The graph repeats until:

- the post is approved, or
- the app reaches 3 attempts

## Limitations

- It is a command-line workflow rather than a web app
- It depends on external API keys from Groq and Tavily
- The generated content is subject to model quality and review constraints
- The loop is intentionally capped at 3 attempts to keep execution bounded

## Typical output

The program prints information such as:

```text
[Verdict: REJECTED]
[Feedback: The hook is strong, but the post needs a clearer takeaway and a stronger CTA.]
```

Then it tries again with revised content until approval or the max number of attempts is reached.

## License

No explicit license file is present in the repository, so usage rights are currently unspecified unless otherwise stated by the project owner.

## Status

This is a small experimental AI workflow project intended for iterative content generation and review. It is suitable for local experimentation and extension.
