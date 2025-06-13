# Comprehensive Guide to Using the Zen MCP Server

## Table of Contents
1. [Overview](#overview)
2. [Available Tools](#available-tools)
3. [Model Selection and Auto Mode](#model-selection-and-auto-mode)
4. [Best Practices](#best-practices)
5. [Common Workflows](#common-workflows)
6. [Advanced Features](#advanced-features)
7. [Troubleshooting](#troubleshooting)

## Overview

### What is Zen MCP Server?

The Zen MCP (Model Context Protocol) Server is an orchestration layer that gives Claude access to multiple AI models (Gemini, O3, OpenRouter models) for enhanced development capabilities. It's designed as a "super-glue" that allows Claude to:

- **Orchestrate between different AI models** - Claude remains in control but can delegate specific tasks to the best model for each job
- **Enable true AI-to-AI conversations** - Models can have multi-turn conversations with context preservation
- **Provide specialized expertise** - Each tool has a specific purpose with tailored system prompts
- **Handle large contexts** - Bypass MCP's 25K token limit through intelligent file handling

### Core Purpose

Think of it as "Claude Code for Claude Code" - it doesn't do magic, it just provides Claude with additional AI perspectives and capabilities. You (the user) remain the orchestrator, guiding Claude on when and how to use these additional models.

## Available Tools

The server provides 7 specialized tools, each with a specific purpose:

### 1. `chat` - General Development Chat & Collaborative Thinking
**Purpose**: Your AI thinking partner for brainstorming, discussions, and general development questions.

**When to use**:
- Bouncing ideas during analysis
- Getting second opinions on plans
- Collaborative brainstorming
- Validating approaches and checklists
- General development questions
- Technology comparisons

**Key features**:
- Balanced temperature (0.5) for creative yet focused responses
- Can reference files for context
- Supports web search for current documentation
- Default thinking mode: medium

**Example usage**:
```
"Chat with zen about the best database for my real-time chat application"
"Use gemini to brainstorm scaling strategies for our API"
"Discuss authentication approaches with pro using files from /src/auth/"
```

### 2. `thinkdeep` - Extended Reasoning Partner
**Purpose**: Get deeper analysis and second opinions to augment Claude's thinking.

**When to use**:
- Need to extend Claude's analysis
- Want to challenge assumptions
- Exploring edge cases
- Validating architectural decisions
- Complex problem-solving requiring deep reasoning

**Key features**:
- High temperature (0.7) for creative exploration
- Default thinking mode: high (16K tokens for Gemini)
- Enhanced critical evaluation of suggestions
- Can request additional context dynamically

**Example usage**:
```
"Think deeper about this microservices design with pro using max thinking mode"
"Use zen to think deeply about potential security vulnerabilities in this architecture"
"Get o3 to think deeper about the logical flow in this algorithm"
```

### 3. `codereview` - Professional Code Review
**Purpose**: Comprehensive code analysis with prioritized, actionable feedback.

**When to use**:
- Finding bugs and issues in code
- Security vulnerability assessment
- Performance optimization opportunities
- Code quality and maintainability checks
- Enforcing coding standards

**Key features**:
- Issues prioritized by severity (🔴 CRITICAL → 🟢 LOW)
- Specialized review types: full, security, performance, quick
- Low temperature (0.2) for focused analysis
- Can review entire directories recursively
- Default thinking mode: medium

**Example usage**:
```
"Perform a security codereview of /src/auth/ with gemini pro"
"Use zen to review api.py focusing on performance issues"
"Get flash to do a quick style check on utils.py"
```

### 4. `precommit` - Pre-Commit Validation
**Purpose**: Comprehensive review of git changes before committing.

**When to use**:
- Before making commits to ensure quality
- Validating changes against requirements
- Finding incomplete implementations
- Multi-repository change validation
- Ensuring no regressions introduced

**Key features**:
- Recursive repository discovery
- Reviews staged and unstaged changes
- Can compare against branches/tags
- Smart diff truncation for large changes
- Validates implementation matches intent

**Example usage**:
```
"Use zen for a precommit review ensuring no security issues"
"Perform precommit with pro using high thinking mode for this critical release"
"Use gemini to precommit and verify all requirements are met"
```

### 5. `debug` - Expert Debugging Assistant
**Purpose**: Root cause analysis for errors and issues.

**When to use**:
- Runtime errors and exceptions
- Logic errors in algorithms
- Performance bottlenecks
- Complex debugging scenarios
- When you need systematic hypothesis generation

**Key features**:
- Generates ranked hypotheses
- Low temperature (0.2) for systematic analysis
- Can reference relevant files
- Accepts stack traces and logs
- Provides validation steps for solutions

**Example usage**:
```
"Debug this TypeError with zen: 'NoneType' object has no attribute 'split'"
"Use o3 to debug the logic error in sort_algorithm.py"
"Get pro to debug why the API returns 500 errors, here's the stack trace: [...]"
```

### 6. `analyze` - Smart File Analysis
**Purpose**: General-purpose code understanding and exploration.

**When to use**:
- Understanding how code works
- Architecture analysis
- Pattern identification
- Dependency analysis
- Code quality assessment

**Key features**:
- Supports specialized analysis types: architecture, performance, security, quality
- Can analyze entire directories
- Medium temperature (0.5) by default
- Identifies patterns and anti-patterns
- Suggests refactoring opportunities

**Example usage**:
```
"Use zen to analyze the architecture of /src/"
"Get gemini to analyze main.py and explain the data flow"
"Use o3 for logical analysis of the algorithm in core.py"
```

### 7. `get_version` - Server Information
**Purpose**: Get server version and configuration details.

**Example usage**:
```
"Get zen to show its version"
```

## Model Selection and Auto Mode

### Auto Mode (Recommended)

When `DEFAULT_MODEL=auto` (default), Claude intelligently selects the best model for each task based on:
- Task complexity
- Required capabilities
- Model strengths

**How it works**:
- Claude analyzes each request
- Considers the tool being used
- Evaluates complexity and requirements
- Selects optimal model automatically

**Auto mode selections**:
- **Complex architecture/security** → Gemini Pro (deep reasoning)
- **Quick checks/formatting** → Flash (ultra-fast)
- **Logic errors/systematic analysis** → O3 (strong reasoning)
- **Balanced tasks** → O3-mini (good performance/speed ratio)

### Available Models

#### Native Models (Direct API Access)

| Model | Alias | Context | Best For |
|-------|-------|---------|----------|
| Gemini 2.5 Pro | `pro` | 1M tokens | Deep analysis, extended thinking (up to 32K tokens) |
| Gemini 2.0 Flash | `flash` | 1M tokens | Quick checks, simple analysis, fast responses |
| O3 | `o3` | 200K tokens | Logic problems, systematic analysis |
| O3-mini | `o3-mini` | 200K tokens | Balanced performance and speed |

#### OpenRouter Models (If Configured)

Access to GPT-4, Claude, Mistral, and many more through OpenRouter API. Popular aliases include:
- `opus`, `sonnet`, `haiku` (Claude models)
- `gpt4o`, `4o-mini` (OpenAI models)
- `mistral`, `deepseek`, `perplexity`

### Model Override

You can always override the automatic selection:
```
"Use flash for a quick format check"
"Get pro to do deep security analysis"
"Use o3 to debug this logic error"
"Use opus via zen for comprehensive review" (OpenRouter)
```

### Thinking Modes (Gemini Models Only)

Control the depth of reasoning vs token cost:

| Mode | Token Budget | Use Case |
|------|--------------|----------|
| `minimal` | 128 tokens | Simple tasks, format checks |
| `low` | 2,048 tokens | Basic reasoning |
| `medium` | 8,192 tokens | **Default** - Most tasks |
| `high` | 16,384 tokens | Complex problems |
| `max` | 32,768 tokens | Exhaustive analysis |

**Example**: 
```
"Use pro with max thinking mode for critical security review"
"Get gemini to analyze with minimal thinking for quick check"
```

## Best Practices

### 1. Let Claude Orchestrate
- Describe what you want to achieve
- Let Claude decide when to use Zen tools
- Trust auto mode for model selection
- Override only when you have specific requirements

### 2. Use the Right Tool for the Job
- **Have an error?** → Use `debug`
- **Need to find issues?** → Use `codereview`
- **Want to understand code?** → Use `analyze`
- **Need deeper analysis?** → Use `thinkdeep`
- **Want to discuss?** → Use `chat`
- **Ready to commit?** → Use `precommit`

### 3. Provide Context
- Always use absolute file paths
- Include relevant files for better analysis
- Provide error messages and stack traces for debugging
- Share your requirements for validation

### 4. Manage Token Usage
- Use lower thinking modes for simple tasks
- Reserve `high` and `max` modes for complex problems
- Consider using Flash for quick checks
- Be aware that higher thinking = more tokens = higher cost

### 5. Leverage Continuation
- Use continuation IDs for multi-turn conversations
- Context carries forward across tools
- Maximum 5 exchanges per conversation thread
- Threads expire after 1 hour

### 6. Web Search Integration
- Enabled by default for current information
- Models suggest searches for Claude to perform
- Particularly useful for:
  - Current documentation
  - Best practices
  - Framework updates
  - Known issues and solutions

## Common Workflows

### 1. Design → Review → Implement
```
"Think deeply about designing a REST API for user management. Get feedback from o3, then help me implement it. After implementation, get a code review from pro."
```

**Flow**:
1. Claude designs initial API
2. O3 provides logical analysis
3. Claude implements based on feedback
4. Gemini Pro reviews the implementation

### 2. Debug → Analyze → Fix → Validate
```
"Debug this authentication error with zen. Once we understand the issue, analyze the auth module to see if there are related problems. Fix everything and then do a precommit check."
```

**Flow**:
1. Debug tool identifies root cause
2. Analyze tool examines related code
3. Claude implements fixes
4. Precommit validates all changes

### 3. Explore → Brainstorm → Decide
```
"Chat with zen about database options for our real-time app. Think deeper about the top 2 options with pro, then help me make a decision."
```

**Flow**:
1. Chat explores options broadly
2. Thinkdeep analyzes top candidates
3. Claude synthesizes recommendations

### 4. Multi-Model Collaboration
```
"Analyze this codebase with flash for a quick overview, then use pro to think deeply about the architecture, and finally get o3 to review the business logic."
```

**Flow**:
1. Flash provides fast initial analysis
2. Pro does deep architectural review
3. O3 focuses on logical correctness
4. Claude synthesizes all perspectives

## Advanced Features

### 1. AI-to-AI Conversation Threading
- Models can ask Claude for clarification
- Claude can provide additional context
- Conversations maintain full history
- Cross-tool continuation supported
- Up to 5 exchanges per thread

**Example flow**:
1. Gemini: "I need to see the config file to understand this error"
2. Claude provides the file
3. Gemini continues analysis with full context

### 2. Large Prompt Handling
- MCP has a 25K token limit
- Server automatically detects large prompts
- Claude saves prompt to `prompt.txt`
- Server reads file directly
- Bypasses MCP limitations

### 3. Dynamic Model Selection
- Auto mode picks models based on:
  - Task complexity
  - Required capabilities
  - Performance needs
  - Cost considerations

### 4. File Deduplication
- Prevents embedding same files multiple times
- Tracks files in conversation history
- Optimizes token usage
- Maintains logical access to all files

### 5. Intelligent Error Recovery
- Automatic retry on transient errors
- Graceful degradation
- Clear error messages
- Maintains conversation context

## Troubleshooting

### Common Issues

**"Model parameter is required"**
- You're in auto mode but didn't specify a model
- Solution: Let Claude pick: "Use zen to analyze..." (Claude chooses model)
- Or specify: "Use pro to analyze..."

**"All file paths must be absolute"**
- Always use full paths starting with `/`
- Wrong: `./src/main.py`
- Right: `/Users/you/project/src/main.py`

**"Prompt too large for MCP"**
- Your prompt exceeds 50K characters
- Claude will automatically save to `prompt.txt`
- Resend with file path

**"Response blocked or incomplete"**
- Model safety filters triggered
- Try rephrasing the request
- Use a different model

### Performance Tips

1. **Use Flash for simple tasks** - 10x faster than Pro
2. **Batch related requests** - Use continuation for follow-ups
3. **Choose appropriate thinking modes** - Don't use `max` for simple tasks
4. **Let auto mode optimize** - It balances performance and quality

### Docker-Specific Issues

**Container not running**:
```bash
docker compose ps  # Check status
docker compose up -d  # Start services
```

**Connection issues**:
```bash
docker exec -i zen-mcp-server echo "test"  # Test connection
docker compose logs zen-mcp  # Check logs
```

## Summary

The Zen MCP Server is a powerful orchestration tool that extends Claude's capabilities through multi-model collaboration. Key principles:

1. **You orchestrate** - Guide Claude on when to use Zen
2. **Claude controls** - Claude manages the tool execution
3. **Models collaborate** - Each provides unique strengths
4. **Context preserves** - Conversations maintain history
5. **Tools specialize** - Each tool has a specific purpose

Use it to get multiple perspectives, deeper analysis, and enhanced development capabilities while Claude remains your primary development partner.