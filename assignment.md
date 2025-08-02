# Founding Engineer (Backend) - Take Home Assignment

Build an autonomous code review agent system that uses AI to analyze GitHub pull requests. The system should implement a goal-oriented AI agent that can plan and execute code reviews independently, process them asynchronously using Celery, and interact with developers through a structured API.

## Core Requirements

### 1. Basic API Endpoints

Create a FastAPI application with the following endpoints:

- POST /analyze-pr : Accept GitHub PR details (repo, PR number)
- GET /status/<task_id> : Check the status of an analysis task
- GET /results/<task_id> : Retrieve the analysis results

### 2. Asynchronous Processing

- Use Celery to handle the code analysis tasks asynchronously
- Implement proper task status tracking and error handling
- Store task results in Redis or PostgreSQL

### 3. AI Agent Implementation

The agent should analyze code for:
- Code style and formatting issues
- Potential bugs or errors
- Performance improvements

## Best Practices

- Use any agent framework from langgraph, crewai, autogen, etc.
- Build required tooling for to fetch code, diff, etc.

## Bonus Points

- Live deployment link
- Docker configuration
- Basic caching of API results
- Meaningful logging
- Support for different programming languages

## Technical Requirements

- Python 3.8+
- FastAPI
- Celery
- Redis or PostgreSQL
- Any LLM API or Use Ollama to run language models locally using ollama (https://ollama.com/download)
- pytest for testing

## Overview

Build an autonomous code review agent system that uses AI to analyze GitHub pull requests, process them asynchronously, and provide structured feedback through an API.

## Time Expectation

- Expected time: 4-6 hours
- Maximum time allowed: 48 hours

## Core Requirements

### 1. API Endpoints (FastAPI)

POST /analyze-pr

{
  "repo_url": "https://github.com/user/repo",
  "pr_number": 123,
  "github_token": "optional_token"
}

GET /status/<task_id>

GET /results/<task_id>

### 2. Asynchronous Processing

- Use Celery for async task processing
- Store results in Redis or PostgreSQL
- Implement task status tracking (pending, processing, completed, failed)
- Handle errors gracefully

### 3. AI Agent Implementation

Use any agent framework (langchain, crewai, litellm, autogen):
The agent should analyze code for:
- Code style and formatting issues
- Potential bugs or errors
- Performance improvements

## Best practices - Expected output format:

{
    "task_id": "abc123",
    "status": "completed",
    "results": {
        "files": [
            {
                "name": "main.py",
                "issues": [
                    {
                        "type": "style",
                        "line": 15,
                        "description": "Line too long",
                        "suggestion": "Break line into multiple lines"
                    },
                    {
                        "type": "bug",
                        "line": 23,
                        "description": "Potential null pointer",
                        "suggestion": "Add null check"
                    }
                ]
            }
        ],
        "summary": {
            "total_files": 1,
            "total_issues": 2,
            "critical_issues": 1
        }
    }
}

## Bonus Points

- Live deployment (e.g., Railway, Render, etc.)
- Docker configuration
- Result caching system
- Structured logging
- Multi-language support
- Rate limiting
- GitHub webhook support

## Technical Requirements

- Python 3.8+
- FastAPI
- Celery
- Redis or PostgreSQL
- Choice of: Any LLM API (OpenAI, Anthropic, etc.), Ollama for local model running
- pytest for testing

## Submission Requirements

1. GitHub repository with:
    - Complete source code
    - README.md including:
        - Project setup instructions
        - API documentation
        - Design decisions
        - Future improvements
    - Requirements.txt or poetry.lock
    - Example environment file (.env.example)
    - Test instructions

2. If deployed:
    - Live API URL
    - Example cURL commands
