# JIRA Skill for Claude Code

[![Agent Skill Standard](https://img.shields.io/badge/Agent%20Skill%20Standard-Compatible-blue)](https://agentskills.io/)
[![Platforms](https://img.shields.io/badge/Platforms-14%2B%20AI%20Agents-green)](https://skillzwave.ai/platforms/)
[![License](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

A comprehensive Claude Code skill that provides intelligent guidance for managing JIRA issues, projects, and workflows through the Atlassian MCP server.

## Table of Contents

- [What is a Skill?](#what-is-a-skill)
- [How This Skill Works](#how-this-skill-works)
- [Installing with Skilz](#installing-with-skilz-universal-installer)
- [Manual Installation](#manual-installation)
- [Prerequisites](#prerequisites)
- [Quick Start](#quick-start)
- [Features](#features)
- [Common Workflows](#common-workflows)
- [Best Practices](#best-practices)
- [Troubleshooting](#troubleshooting)
- [Contributing](#contributing)

---

## What is a Skill?

A **skill** is an instruction manual that teaches Claude Code how to use MCP (Model Context Protocol) tools effectively. Think of it this way:

- **MCP Server** (Atlassian MCP) = The tool that provides access to JIRA APIs
- **Skill** (this repository) = The instruction manual that guides Claude on best practices, workflows, and patterns for using that tool

Claude Code can discover and use MCP tools automatically, but skills provide the critical context, workflows, and domain expertise that make interactions efficient, reliable, and consistent with your team's practices.

## How This Skill Works

This skill works hand-in-glove with the **Atlassian MCP server** (`mcp__atlassian`). The MCP provides raw access to JIRA's API capabilities, while this skill provides:

- **Structured workflows** for common JIRA operations
- **Field discovery patterns** for handling custom fields
- **JQL query construction** guidance and examples
- **Best practices** for issue creation, transitions, and agile operations
- **Troubleshooting guides** for common errors
- **Validation patterns** to ensure reliable operations

When you ask Claude Code to work with JIRA, this skill ensures operations follow proven patterns, validate inputs properly, and handle JIRA's complexity gracefully.

---

## Installing with Skilz (Universal Installer)

The recommended way to install this skill across different AI coding agents is using the **skilz** universal installer.

### Install Skilz

```bash
pip install skilz
```

This skill supports [Agent Skill Standard](https://agentskills.io/) which means it supports 14 plus coding agents including Claude Code, OpenAI Codex, Cursor and Gemini.


### Git URL Options

You can use either `-g` or `--git` with HTTPS or SSH URLs:

```bash
# HTTPS URL
skilz install -g https://github.com/SpillwaveSolutions/jira

# SSH URL
skilz install --git git@github.com:SpillwaveSolutions/jira.git
```

### Claude Code

Install to user home (available in all projects):
```bash
skilz install -g https://github.com/SpillwaveSolutions/jira
```

Install to current project only:
```bash
skilz install -g https://github.com/SpillwaveSolutions/jira --project
```

### OpenCode

Install for [OpenCode](https://opencode.ai):
```bash
skilz install -g https://github.com/SpillwaveSolutions/jira --agent opencode
```

Project-level install:
```bash
skilz install -g https://github.com/SpillwaveSolutions/jira --project --agent opencode
```

### Gemini

Project-level install for Gemini:
```bash
skilz install -g https://github.com/SpillwaveSolutions/jira --agent gemini
```

### OpenAI Codex

Install for OpenAI Codex:
```bash
skilz install -g https://github.com/SpillwaveSolutions/jira --agent codex
```

Project-level install:
```bash
skilz install -g https://github.com/SpillwaveSolutions/jira --project --agent codex
```


### Install from Skillzwave Marketplace
```
# Claude to user home dir ~/.claude/skills
skilz install SpillwaveSolutions_jira/jira

# Claude skill in project folder ./claude/skills
skilz install SpillwaveSolutions_jira/jira --project

# OpenCode install to user home dir ~/.config/opencode/skills
skilz install SpillwaveSolutions_jira/jira --agent opencode

# OpenCode project level
skilz install SpillwaveSolutions_jira/jira --agent opencode --project

# OpenAI Codex install to user home dir ~/.codex/skills
skilz install SpillwaveSolutions_jira/jira

# OpenAI Codex project level ./.codex/skills
skilz install SpillwaveSolutions_jira/jira --agent opencode --project


# Gemini CLI (project level) -- only works with project level
skilz install SpillwaveSolutions_jira/jira --agent gemini

```

See this site [skill Listing](https://skillzwave.ai/skill/SpillwaveSolutions__jira__jira__SKILL/) to see how to install this exact skill to 14+ different coding agents.


### Other Supported Agents

Skilz supports 14+ coding agents including Claude Code, OpenAI Codex, OpenCode, Cursor, Gemini CLI, GitHub Copilot CLI, Windsurf, Qwen Code, Aidr, and more.

For the full list of supported platforms, visit [SkillzWave.ai/platforms](https://skillzwave.ai/platforms/) or see the [skilz-cli GitHub repository](https://github.com/SpillwaveSolutions/skilz-cli)


<a href="https://skillzwave.ai/">Largest Agentic Marketplace for AI Agent Skills</a> and
<a href="https://spillwave.com/">SpillWave: Leaders in AI Agent Development.</a>

---

## Manual Installation

### Installation Levels

This skill can be installed at multiple levels depending on your organizational structure and needs:

#### 1. Global Installation (User Level)

Install in your home directory for use across all projects:

```bash
~/.claude/skills/jira/
```

**Use case**: You work with a single JIRA instance across all projects.

#### 2. Project-Level Installation

Install within a specific project directory:

```bash
/path/to/project/.claude/skills/jira/
```

**Use case**: Project-specific JIRA configuration or custom workflows that differ from other projects.

#### 3. Workspace-Level Installation

Install at a workspace directory that groups multiple related projects:

```bash
~/workspace/acme-corp/.claude/skills/jira/
~/workspace/tech-startup/.claude/skills/jira/
```

**Use case**:
- **Client-based workspaces**: Different skills/configs for different clients
- **Department-based workspaces**: Engineering vs Operations vs Support teams
- **Company-based workspaces**: Multiple clients with different JIRA instances

### Installation Priority

Claude Code follows this priority order when loading skills:
1. **Project-level** (`.claude/skills/` in current directory)
2. **Workspace-level** (`.claude/skills/` in parent directories)
3. **Global-level** (`~/.claude/skills/` in home directory)

This allows project-specific customizations to override workspace or global defaults.

---

## Multi-Instance JIRA Support

For organizations that need to connect to multiple JIRA instances (multiple clients, acquisitions, different departments), you can configure the Atlassian MCP at different levels using `.mcp.json` files.

### Example: Multiple Client Workspaces

**Scenario**: You're a consultant working with multiple clients, each with their own Atlassian instance.

```bash
# Client 1 workspace
~/clients/acme-industries/
├── .mcp.json              # JIRA config for acme-industries.atlassian.net
├── .claude/
│   ├── skills/jira/       # Client-specific JIRA workflows (optional)
│   └── settings.local.json
├── project-alpha/
└── project-beta/

# Client 2 workspace
~/clients/globex-corp/
├── .mcp.json              # JIRA config for globex.atlassian.net
├── .claude/
│   ├── skills/jira/       # Client-specific JIRA workflows (optional)
│   └── settings.local.json
├── web-app/
└── mobile-app/
```

### Example: Department-Based Workspaces

**Scenario**: Large organization with different JIRA instances per department.

```bash
# Engineering workspace
~/workspaces/engineering/
├── .mcp.json              # JIRA config for eng.company.atlassian.net
├── .claude/skills/jira/   # Engineering-specific workflows
├── backend-services/
└── frontend-apps/

# Operations workspace
~/workspaces/operations/
├── .mcp.json              # JIRA config for ops.company.atlassian.net
├── .claude/skills/jira/   # Operations-specific workflows
├── infrastructure/
└── monitoring/
```

### .mcp.json Configuration

Each workspace can have its own `.mcp.json` file with JIRA credentials:

```json
{
  "mcpServers": {
    "atlassian": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-atlassian"],
      "env": {
        "JIRA_URL": "https://acme-industries.atlassian.net",
        "JIRA_API_TOKEN": "your-api-token-here",
        "JIRA_EMAIL": "your-email@acme-industries.com",
        "JIRA_PROJECTS_FILTER": "ENG,API,WEB"
      }
    }
  }
}
```

### Configuration Priority

Claude Code uses this priority for `.mcp.json` files:
1. **Project directory** (most specific)
2. **Workspace directory** (parent directories)
3. **Global config** (`~/.claude/mcp.json`)

This allows you to:
- Connect to different JIRA instances per workspace
- Use different credentials per client/department
- Override global JIRA settings for specific projects
- Maintain separate JIRA configurations without conflicts

---

## Prerequisites

### Required MCP Server

The **Atlassian MCP server** must be configured in Claude Code:

```bash
npm install -g @modelcontextprotocol/server-atlassian
```

Configure in `~/.claude/mcp.json` or workspace-level `.mcp.json`:

```json
{
  "mcpServers": {
    "atlassian": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-atlassian"],
      "env": {
        "JIRA_URL": "https://your-domain.atlassian.net",
        "JIRA_API_TOKEN": "your-api-token",
        "JIRA_EMAIL": "your-email@example.com",
        "JIRA_PROJECTS_FILTER": "PROJ1,PROJ2"
      }
    }
  }
}
```

### JIRA API Token

Generate a JIRA API token:
1. Go to https://id.atlassian.com/manage-profile/security/api-tokens
2. Click "Create API token"
3. Copy the token and add to your `.mcp.json` configuration

### Permissions

Ensure your JIRA account has appropriate permissions for:
- Creating/updating issues
- Searching issues
- Managing sprints/epics (if using Agile features)
- Transitioning issues through workflows

---

## Quick Start

### 1. Install the Skill

Using Skilz (recommended):
```bash
pip install skilz
skilz install -g https://github.com/SpillwaveSolutions/jira
```

Or manually:
```bash
# Global installation
mkdir -p ~/.claude/skills/
cd ~/.claude/skills/
git clone https://github.com/SpillwaveSolutions/jira.git jira
```

### 2. Configure Atlassian MCP

Create or update `.mcp.json` at the appropriate level:

```json
{
  "mcpServers": {
    "atlassian": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-atlassian"],
      "env": {
        "JIRA_URL": "https://your-domain.atlassian.net",
        "JIRA_API_TOKEN": "your-token-here",
        "JIRA_EMAIL": "your-email@example.com"
      }
    }
  }
}
```

### 3. Start Using JIRA with Claude Code

Simply ask Claude Code to work with JIRA:

```
"Create a JIRA task in project ENG for implementing user authentication"
"Search JIRA for all open bugs assigned to me"
"Move ticket ENG-123 to Done"
"Show me the active sprint for our board"
```

Claude Code will automatically:
- Validate project keys
- Discover custom fields
- Construct proper JQL queries
- Handle workflows and transitions
- Follow best practices from this skill

---

## Features

### Issue Management
- Create issues (Task, Bug, Story, Epic, Subtask)
- Update issue fields (priority, assignee, custom fields)
- Search with JQL (comprehensive query patterns)
- Get issue details
- Delete issues
- Batch create multiple issues

### Workflow Operations
- Get available transitions for an issue
- Transition issues between states
- Add required fields during transitions
- Handle workflow-specific requirements

### Agile/Scrum
- Work with boards and sprints
- Create and update sprints
- Move issues to sprints
- Track epic progress
- Link issues to epics
- Sprint planning workflows

### Collaboration
- Add comments with Markdown support
- Upload attachments
- Create issue links (Blocks, Relates, Duplicates)
- Create remote links (Confluence, web URLs)

### Custom Field Discovery
- Search fields by keyword
- Discover custom field IDs and types
- Validate field formats
- Handle project-specific fields

### Batch Operations
- Create multiple issues efficiently
- Get changelogs for multiple issues
- Bulk operations on issue sets

---

## File Structure

```
~/.claude/skills/jira/
├── CLAUDE.md                     # Architecture guide for Claude Code
├── README.md                     # This file
├── SKILL.md                      # Detailed skill documentation
├── templates/
│   └── issue_creation.json       # Issue creation templates
├── references/
│   └── jql_guide.md             # Comprehensive JQL reference
├── scripts/                      # Utility scripts (future)
└── assets/                       # Additional resources
```

## Key Documentation

### SKILL.md (Primary Reference)
Comprehensive workflow documentation including:
- Issue creation workflow (step-by-step)
- Custom field discovery methodology
- JQL query patterns
- Agile/Scrum operations
- Troubleshooting guide
- Best practices

### references/jql_guide.md
Complete JQL (JIRA Query Language) reference:
- All operators and functions
- Date/time queries
- Historical search operators
- Sprint and epic queries
- 50+ common use cases
- Best practices and common pitfalls

### templates/issue_creation.json
Standard templates for:
- Basic tasks
- Bug reports with reproduction steps
- User stories with acceptance criteria
- Epics
- Subtasks
- Issues with custom fields
- Batch creation examples

### CLAUDE.md
Architecture and patterns guide for Claude Code instances, documenting:
- Core workflow patterns
- Critical validation steps
- Custom field discovery methodology
- Common command patterns

---

## Common Workflows

### Creating Issues

```
"Create a JIRA task in project ENG for implementing rate limiting"
"Create a bug in project API: Login endpoint returns 500 error"
"Create an epic in project MOBILE for offline support feature"
```

### Searching Issues

```
"Search JIRA for all open bugs in project ENG assigned to me"
"Find all issues in the current sprint"
"Show me overdue issues in project API"
"Find all stories without acceptance criteria"
```

### Managing Workflows

```
"Move ENG-123 to In Progress"
"Transition API-456 to Done"
"Show available transitions for MOBILE-789"
```

### Agile Operations

```
"Show me the active sprint for board 1000"
"Create a sprint named 'Sprint 15' for board 1000"
"Add ENG-123 and ENG-124 to the current sprint"
"Link story ENG-200 to epic ENG-100"
```

### Custom Field Discovery

```
"Find custom fields with keyword 'owning team'"
"Search for fields containing 'acceptance criteria'"
"What custom fields are available in project ENG?"
```

---

## Best Practices

### 1. Always Validate Project Keys
Never assume project keys - always verify before operations:
```
"What projects are available in JIRA?"
```

### 2. Discover Custom Fields
Use field search before working with custom fields:
```
"Find custom fields with keyword 'story points'"
```

### 3. Use JQL for Complex Searches
Leverage JQL functions and operators:
```
"Search JIRA with: project = ENG AND status = 'In Progress' AND assignee = currentUser()"
```

### 4. Batch Operations for Efficiency
Create multiple related tickets at once:
```
"Create 5 tasks in project ENG for: setup, config, test, docs, deploy"
```

### 5. Link Related Issues
Always link related work:
```
"Create issue link: ENG-123 blocks ENG-124"
```

---

## Troubleshooting

### "Project not found"
- Use `"What projects are available?"` to see available projects
- Check `JIRA_PROJECTS_FILTER` environment variable in `.mcp.json`
- Verify project key is exact (case-sensitive)

### "Field required" errors
- Ask: `"Search for required fields in project ENG"`
- Check project configuration
- Some fields required by workflow, not project

### "Invalid transition" errors
- Ask: `"Show available transitions for ENG-123"`
- Current status matters for available transitions
- Check permissions

### Custom fields not working
- Ask: `"Find custom fields with keyword 'team'"`
- Use discovered field ID format: `customfield_10010`
- Check if field applies to the issue type

### Multiple JIRA instances
- Verify correct `.mcp.json` is loaded for workspace/project
- Check `JIRA_URL` in environment configuration
- Ensure API token matches the JIRA instance

---

## Integration with Other Skills

This JIRA skill can work alongside other Claude Code skills:

### Meeting Notes Skill
Process action items into JIRA tickets:
```
"Create JIRA tickets from these meeting action items in project ENG"
```

### Project Management Skills
Reference project context when creating tickets:
```
"Create ticket in the project we're currently working on"
```

### Documentation Skills
Link JIRA tickets to documentation:
```
"Create remote link from ENG-123 to our Confluence page"
```

---

## Advanced Usage

### Custom JQL Queries
See `references/jql_guide.md` for:
- 50+ common JQL patterns
- Historical search operators
- Sprint and epic management
- Date functions and relative time
- Best practices and pitfalls

### Batch Operations
See `templates/issue_creation.json` for:
- Batch creation examples
- Field format reference
- Multi-issue workflows

### Automation Patterns
The `scripts/` directory can contain custom automation:
- Bulk issue creation from CSV
- Sprint reports
- Issue analysis and metrics
- Custom workflows

---

## Updates and Maintenance

### Updating the Skill

```bash
cd ~/.claude/skills/jira  # or workspace/project path
git pull origin main
```

### Customizing for Your Team

You can customize this skill by:
1. **Modifying templates** in `templates/issue_creation.json`
2. **Adding custom JQL patterns** to `references/jql_guide.md`
3. **Documenting team workflows** in `SKILL.md`
4. **Creating automation scripts** in `scripts/`
5. **Adjusting field mappings** for project-specific custom fields

### Version Control

Keep skill customizations in version control:
```bash
cd ~/.claude/skills/jira  # or workspace path
git remote add team-fork https://github.com/your-org/jira-skill-fork.git
git push team-fork main
```

This allows sharing customizations across your team.

---

## Support

For issues or questions:

1. **Check SKILL.md** for detailed workflows
2. **Review references/jql_guide.md** for JQL help
3. **Consult Atlassian MCP documentation**
4. **Verify .mcp.json configuration**
5. **Check JIRA permissions** for your account
6. **Review CLAUDE.md** for architecture patterns

---

## Contributing

To improve this skill:

1. Document new workflows in `SKILL.md`
2. Add JQL patterns to `references/jql_guide.md`
3. Create templates in `templates/`
4. Share automation scripts in `scripts/`
5. Update best practices based on experience

---

## License

This skill is designed for use with Claude Code and the Atlassian MCP server.

---

## Related Resources

- [Claude Code Documentation](https://docs.claude.com/claude-code)
- [Model Context Protocol](https://modelcontextprotocol.io/)
- [Atlassian JIRA Documentation](https://support.atlassian.com/jira/)
- [JQL Reference](https://support.atlassian.com/jira-software-cloud/docs/use-advanced-search-with-jira-query-language-jql/)
- [Atlassian MCP Server](https://github.com/modelcontextprotocol/servers/tree/main/src/atlassian)
- [SkillzWave Marketplace](https://skillzwave.ai/)
- [SpillWave Solutions](https://spillwave.com/)
