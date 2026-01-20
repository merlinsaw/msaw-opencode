# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

This is **OpenCode** - a terminal-based AI assistant for developers built in Go that provides an interactive TUI interface for AI-powered coding assistance with support for multiple AI providers, session management, and integrated development tools.

## Key Architecture

OpenCode is built with a modular architecture focused on these core systems:

- **CLI Layer**: Cobra-based command interface in `/cmd/` with both interactive TUI and non-interactive prompt modes
- **Application Core**: Core services in `/internal/app/` managing DB, sessions, and LSP integration
- **AI Integration**: LLM providers and agents in `/internal/llm/` supporting 15+ AI models from 9 providers
- **TUI Interface**: Bubble Tea-based terminal UI in `/internal/tui/` with chat, dialogs, and navigation
- **Storage**: SQLite database in `/internal/db/` for sessions, messages, and file metadata tracking
- **LSP Support**: Language Server Protocol integration for real-time code intelligence
- **MCP Integration**: Model Context Protocol for external tool integration

## Development Commands

- **Build**: `go build -o opencode .` or use `./install` script
- **Run**: `./opencode` (interactive), `opencode -p "prompt"` (non-interactive)
- **Test**: `go test ./...`
- **Debug**: `opencode -d` for verbose logging

## Directory Structure

```
cmd/                     # CLI entry points and schema CLI
internal/
  app/                  # Main application orchestration
  config/              # JSON configuration management
  db/                  # SQLite with SQLC for typed queries
  llm/                 # AI provider integrations
    agent/             # AI task orchestration
    models/            # Provider implementations (OpenAI, Claude, Gemini, etc.)
    prompt/            # Prompt templates for different AI agents
    provider/          # Unified provider interface
    tools/             # AI-accessible tools (bash, file operations, search, etc.)
  tui/                 # Terminal UI components
    components/        # Reusable TUI widgets
    layout/            # Screen layouts and overlays
    page/              # Main app pages
    styles/            # Theme support (8 built-in themes)
  lsp/                 # Language Server Protocol client
  session/             # Conversation session management
  message/             # Message and attachment handling
  pubsub/              # Event system for TUI updates
```

## AI Provider Integration

The system supports multiple AI providers through a unified interface:
- **OpenAI**: GPT-4.1, GPT-4o, o1, o3, o4-mini families
- **Anthropic**: Claude 3.5-4 Sonnet/Opus families
- **Google**: Gemini 2.0-2.5, Vertex AI
- **GitHub Copilot**: GPT-4.1, Claude, O3/O4, Gemini
- **AWS Bedrock**: Claude variants
- **Groq**: Llama 4, Qwen models
- **Azure OpenAI**: All GPT variants
- **OpenRouter**: Multi-provider access
- **Local**: Custom endpoint support

## AI Tools Available

The AI agent has access to these development tools:
- File operations: `glob`, `grep`, `ls`, `view`, `write`, `edit`, `patch`
- Shell execution: `bash` with configurable shells
- Diagnostics: `diagnostics` via LSP integration for code analysis
- External data: `fetch` for web resources
- Search: `sourcegraph` for public code search
- Sub-tasking: `agent` for autonomous task handling

## Configuration & Environment

Configuration is managed via JSON files:
- `~/.opencode.json` (user config)
- `./.opencode.json` (project config)
- Environment variables for API keys (OPENAI_API_KEY, ANTHROPIC_API_KEY, etc.)

Key configuration sections:
- `providers`: Enable/disable AI providers and set API keys
- `agents`: Configure which models different agent types use
- `lsp`: Language server configuration per language
- `mcpServers`: Model Context Protocol server configuration
- `shell`: Custom shell configuration for bash tool

## Key Features

- **Session Management**: Persistent conversations with SQLite storage
- **File Change Tracking**: Visual diff tracking during sessions
- **Custom Commands**: Markdown-based predefined prompts with named arguments
- **Auto-compaction**: Automatic session summarization for long conversations
- **LSP Integration**: Real-time code intelligence and diagnostics
- **MCP Support**: Extensible tool integration via standardized protocol
- **Multiple Themes**: 8 built-in color themes (Catppuccin, Dracula, etc.)
- **Non-interactive Mode**: CLI integration for scripts and automation

## Database Schema

Uses SQLC with SQLite schema in `/internal/db/`
- **Sessions**: Conversation sessions with metadata
- **Messages**: Chat messages with attachments
- **Files**: Tracked file information and changes
- **Migrations**: Automatic schema updates via Goose