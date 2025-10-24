# JIRA Management Skill

A comprehensive Claude Code skill for managing JIRA issues, projects, and workflows using the Atlassian MCP server.

## Overview

This skill provides intelligent JIRA management capabilities including:
- Creating and managing issues with proper field validation
- Searching and filtering issues using JQL
- Managing workflows and transitions
- Working with epics, sprints, and agile boards
- Adding comments, attachments, and links
- Batch operations for efficiency
- Project and version management

## Prerequisites

### Required MCP Server
- **Atlassian MCP** (`mcp__atlassian`) must be configured in Claude Code
- JIRA credentials (API token or OAuth) must be set up
- Appropriate JIRA permissions for the operations you need

### Environment Configuration
The Atlassian MCP may use environment variables:
- `JIRA_URL`: Your JIRA instance URL
- `JIRA_API_TOKEN`: API token for authentication
- `JIRA_EMAIL`: Email associated with API token
- `JIRA_PROJECTS_FILTER`: (Optional) Comma-separated project keys to filter

## Skill Workflow

### 1. Issue Creation Workflow

When creating JIRA issues, follow this sequence:

#### Step 1: Gather Requirements
Ask the user for:
- **Project key** (e.g., "PROJ", "DEV", "SUPPORT") - NEVER ASSUME
- **Issue type** (Task, Bug, Story, Epic, Subtask)
- **Summary** (title/description)
- **Priority** (if applicable)
- **Assignee** (optional - email, display name, or account ID)
- **Additional fields** (components, labels, custom fields)

#### Step 2: Validate Project
Use `mcp__atlassian__jira_get_all_projects` to:
- Verify project exists
- Get available projects if user unsure
- Confirm project key matches exactly

#### Step 3: Search Available Fields (if needed)
Use `mcp__atlassian__jira_search_fields` to:
- Find custom field names and IDs
- Understand field requirements
- Example: `mcp__atlassian__jira_search_fields` with keyword "epic"

#### Step 4: Create Issue
Use `mcp__atlassian__jira_create_issue` with:
```json
{
  "project_key": "PROJ",
  "summary": "Implement user authentication",
  "issue_type": "Task",
  "description": "Detailed description in Markdown format",
  "assignee": "user@example.com",
  "components": "Frontend,API",
  "additional_fields": {
    "priority": {"name": "High"},
    "labels": ["security", "authentication"],
    "parent": "PROJ-123"
  }
}
```

#### Step 5: Follow-up Actions (if needed)
- Link to epic: `mcp__atlassian__jira_link_to_epic`
- Add attachments: Include in update or create
- Add comments: `mcp__atlassian__jira_add_comment`
- Create issue links: `mcp__atlassian__jira_create_issue_link`

### 2. Issue Search and Management

#### Searching Issues
Use `mcp__atlassian__jira_search` with JQL:

**Note:** For comprehensive JQL documentation including advanced queries, historical operators,
sprint/epic management, date functions, and best practices, see `references/jql_guide.md`.

**Common JQL Patterns:**
```jql
# Find all open issues in a project
project = PROJ AND status = Open

# Find your assigned issues
assignee = currentUser() AND status != Done

# Find recently updated issues
updated >= -7d AND project = PROJ

# Find issues in Epic
parent = PROJ-123

# Find issues by label
labels = frontend AND project = PROJ

# Complex query
project = PROJ AND (status = "In Progress" OR status = "In Review") AND priority = High

# Issues in current sprint
sprint IN openSprints()

# Historical search - issues that were blocked
status WAS "Blocked"

# Issues changed in last week
status CHANGED AFTER -7d
```

Parameters:
- `jql`: JQL query string
- `fields`: "summary,status,assignee,priority" or "*all"
- `limit`: Max results (1-50, default 10)
- `start_at`: Pagination offset

#### Getting Issue Details
Use `mcp__atlassian__jira_get_issue`:
- `issue_key`: "PROJ-123"
- `fields`: Comma-separated or "*all"
- `expand`: "renderedFields", "transitions", "changelog"
- `comment_limit`: Number of comments to include

#### Updating Issues
Use `mcp__atlassian__jira_update_issue`:
```json
{
  "issue_key": "PROJ-123",
  "fields": {
    "summary": "Updated summary",
    "assignee": "user@example.com",
    "priority": {"name": "Critical"}
  },
  "additional_fields": {
    "labels": ["urgent", "hotfix"]
  },
  "attachments": "/path/to/file1.txt,/path/to/file2.pdf"
}
```

### 3. Workflow and Transitions

#### Get Available Transitions
Use `mcp__atlassian__jira_get_transitions`:
- Returns list of valid transitions for current issue state
- Each transition has an ID and name

#### Transition Issue
Use `mcp__atlassian__jira_transition_issue`:
```json
{
  "issue_key": "PROJ-123",
  "transition_id": "31",
  "fields": {
    "resolution": {"name": "Fixed"}
  },
  "comment": "Moving to Done after completing implementation"
}
```

### 4. Agile/Scrum Operations

#### Working with Boards
1. **Get boards**: `mcp__atlassian__jira_get_agile_boards`
   - Filter by: `board_name`, `project_key`, `board_type` (scrum/kanban)

2. **Get board issues**: `mcp__atlassian__jira_get_board_issues`
   - Requires `board_id` and `jql` filter

#### Working with Sprints
1. **Get sprints**: `mcp__atlassian__jira_get_sprints_from_board`
   - Filter by state: "active", "future", "closed"

2. **Get sprint issues**: `mcp__atlassian__jira_get_sprint_issues`
   - Returns all issues in specified sprint

3. **Create sprint**: `mcp__atlassian__jira_create_sprint`
```json
{
  "board_id": "1000",
  "sprint_name": "Sprint 15",
  "start_date": "2025-01-21T09:00:00.000+0000",
  "end_date": "2025-02-04T17:00:00.000+0000",
  "goal": "Complete authentication feature"
}
```

4. **Update sprint**: `mcp__atlassian__jira_update_sprint`

#### Working with Epics
1. **Find epics**: Use JQL: `issuetype = Epic AND project = PROJ`
2. **Link to epic**: `mcp__atlassian__jira_link_to_epic`
3. **Find issues in epic**: Use JQL: `parent = EPIC-KEY`

### 5. Linking and Relationships

#### Issue Links
Use `mcp__atlassian__jira_create_issue_link`:
```json
{
  "link_type": "Blocks",
  "inward_issue_key": "PROJ-123",
  "outward_issue_key": "PROJ-456",
  "comment": "This issue blocks the other",
  "comment_visibility": {"type": "group", "value": "jira-users"}
}
```

Get link types: `mcp__atlassian__jira_get_link_types`

#### Remote Links (Web/Confluence)
Use `mcp__atlassian__jira_create_remote_issue_link`:
```json
{
  "issue_key": "PROJ-123",
  "url": "https://confluence.example.com/pages/123456",
  "title": "Technical Design Doc",
  "summary": "Detailed architecture documentation",
  "relationship": "documentation"
}
```

### 6. Comments and Collaboration

#### Add Comment
Use `mcp__atlassian__jira_add_comment`:
- Supports Markdown format
- Visible in issue activity

#### Get Comments
Use `mcp__atlassian__jira_get_issue` with appropriate fields

### 7. Batch Operations

#### Batch Create Issues
Use `mcp__atlassian__jira_batch_create_issues`:
```json
{
  "issues": "[{\"project_key\":\"PROJ\",\"summary\":\"Task 1\",\"issue_type\":\"Task\"},{\"project_key\":\"PROJ\",\"summary\":\"Task 2\",\"issue_type\":\"Bug\"}]",
  "validate_only": false
}
```

#### Batch Get Changelogs
Use `mcp__atlassian__jira_batch_get_changelogs`:
- Get history for multiple issues
- Filter by specific fields
- Cloud only feature

### 8. Project and Version Management

#### List Projects
Use `mcp__atlassian__jira_get_all_projects`:
- Returns accessible projects
- Respects JIRA_PROJECTS_FILTER if configured

#### Get Project Issues
Use `mcp__atlassian__jira_get_project_issues`:
- Returns all issues for specific project
- Supports pagination

#### Version Management
1. **Get versions**: `mcp__atlassian__jira_get_project_versions`
2. **Create version**: `mcp__atlassian__jira_create_version`
3. **Batch create**: `mcp__atlassian__jira_batch_create_versions`

## Best Practices

### 1. Always Validate Input
- Never assume project keys - always verify
- Use `jira_search_fields` to find custom field IDs
- Check available transitions before transitioning

### 2. Use Appropriate Field Selection
- Request only needed fields to reduce token usage
- Use `"*all"` sparingly, only when necessary
- Specify fields explicitly for better performance

### 3. JQL Query Construction
- **See `references/jql_guide.md` for comprehensive JQL documentation**
- Use proper JQL syntax with operators: `=`, `!=`, `~`, `>`, `<`, `>=`, `<=`
- Quote values with spaces: `status = "In Progress"`
- Quote personal space keys: `space = "~username"`
- Use functions: `currentUser()`, `startOfDay()`, `startOfWeek()`
- Leverage historical operators: `WAS`, `CHANGED`, `WAS IN`, `WAS NOT`
- Use sprint functions: `openSprints()`, `closedSprints()`, `futureSprints()`

### 4. Error Handling
- Check for required fields before creating issues
- Validate transition IDs before executing
- Handle permission errors gracefully
- Provide clear error messages to user

### 5. Efficiency
- Use batch operations for multiple issues
- Paginate large result sets
- Use JQL filters to reduce result size
- Leverage project filters when applicable

### 6. User Experience
- Always confirm project key with user
- Show created issue key immediately
- Provide links to view in browser when possible
- Summarize results of batch operations

## Common Use Cases

### Use Case 1: Create Story with Subtasks
```
1. Create Epic (if needed)
2. Create Story and link to Epic
3. Create multiple Subtasks with parent = Story key
4. Optionally add to sprint
```

### Use Case 2: Bug Triage Workflow
```
1. Search for bugs: issuetype = Bug AND status = Open
2. For each bug:
   - Update priority
   - Assign to developer
   - Add to sprint
   - Transition to "In Progress"
```

### Use Case 3: Sprint Planning
```
1. Get active sprint
2. Search backlog issues
3. Move selected issues to sprint
4. Update estimates
5. Assign to team members
```

### Use Case 4: Release Management
```
1. Create version for release
2. Search issues: fixVersion = "1.0.0"
3. Verify all are Done
4. Create release notes from issue summaries
5. Update version status
```

### Use Case 5: Epic Tracking
```
1. Create Epic
2. Search and link related issues
3. Get Epic progress (search child issues)
4. Generate status report
```

## Templates

See `templates/` directory for:
- `issue_creation.json`: Standard issue creation templates
- `jql_queries.txt`: Common JQL query patterns
- `workflow_scripts.md`: Common workflow automation scripts

## References

See `references/` directory for:
- `jira_field_reference.md`: Common JIRA fields and their usage
- `jql_guide.md`: Comprehensive JQL query guide
- `workflow_patterns.md`: Common workflow patterns

## Integration Examples

See `scripts/` directory for:
- `bulk_issue_creator.py`: Create multiple issues from CSV
- `sprint_reporter.py`: Generate sprint reports
- `issue_analyzer.py`: Analyze issue patterns

## Custom Field Discovery Methodology

JIRA instances heavily customize their field schemas with custom fields that vary by organization, project, and issue type. These fields have opaque identifiers (e.g., `customfield_10352`) that must be discovered and mapped to human-readable names for automation.

### The Challenge

**Problem**: You need to create tickets with specific fields, but you only know the human-readable name (e.g., "Owning Team", "Acceptance Criteria", "Risk Assessment") and not the custom field ID required by the API.

**Symptom**: Creating tickets fails with "field not found" or required fields are silently ignored because you're using the wrong identifier.

### Discovery Workflow

#### Step 1: Search by Keyword

Use `mcp__atlassian__jira_search_fields` to find fields by partial name match:

```json
{
  "keyword": "owning",
  "limit": 10
}
```

**Returns:**
```json
[
  {
    "id": "customfield_11077",
    "name": "Owning Team",
    "custom": true,
    "schema": {
      "type": "option",
      "custom": "com.atlassian.jira.plugin.system.customfieldtypes:select"
    }
  }
]
```

**Key Information Extracted:**
- **Field ID**: `customfield_11077` (use this in API calls)
- **Field Name**: "Owning Team" (human-readable)
- **Field Type**: `option` with `select` custom type (dropdown/single-select)
- **Custom**: `true` (not a standard JIRA field)

#### Step 2: Understand Field Type

The `schema.type` and `schema.custom` fields tell you how to format values:

| Schema Type | Custom Type | Value Format | Example |
|-------------|-------------|--------------|---------|
| `string` | `textarea` | Plain string | `"Risk assessment text"` |
| `number` | `float` | Number | `3` or `3.5` |
| `option` | `select` | Object with value | `{"value": "Team A"}` |
| `array` | `multiselect` | Array of objects | `[{"value": "Label1"}, {"value": "Label2"}]` |
| `user` | - | User identifier | `"user@example.com"` |
| `date` | - | ISO 8601 date | `"2025-01-15"` |
| `datetime` | - | ISO 8601 datetime | `"2025-01-15T10:30:00.000+0000"` |

#### Step 3: Test with Single Issue

Create a test issue using the discovered field ID:

```json
{
  "project_key": "PROJ",
  "summary": "Test custom field",
  "issue_type": "Task",
  "additional_fields": {
    "customfield_11077": {
      "value": "Platform Team"
    }
  }
}
```

**Verify**: Check the created issue in JIRA UI to confirm the field populated correctly.

#### Step 4: Document the Mapping

Once verified, document the field mapping in a project-specific configuration file:

```json
{
  "project_key": "PROJ",
  "custom_fields": {
    "owning_team": {
      "field_id": "customfield_11077",
      "field_name": "Owning Team",
      "field_type": "select",
      "required": true,
      "description": "Team responsible for this work"
    },
    "acceptance_criteria": {
      "field_id": "customfield_10352",
      "field_name": "Acceptance Criteria_gxp",
      "field_type": "textarea",
      "required": true,
      "description": "GXP acceptance criteria"
    },
    "story_points": {
      "field_id": "customfield_10060",
      "field_name": "Story Points",
      "field_type": "number",
      "required": false
    }
  }
}
```

### Advanced Discovery Techniques

#### Discovering Required Fields

Some fields are required by project configuration or workflow rules. To discover these:

1. **Attempt to create without the field** - The error message often reveals required fields
2. **Check project settings** - Use `jira_get_all_projects` and examine field configurations
3. **Inspect existing issues** - Use `jira_get_issue` with `fields=*all` to see all populated fields

#### Discovering Field Value Constraints

For `select`, `multiselect`, or `option` fields, you need to know valid values:

**Method 1: Inspect existing issues**
```bash
# Get issue with all fields
jira_get_issue(issue_key="PROJ-123", fields="*all")

# Look for the custom field in response
# Example: "customfield_11077": {"value": "Platform Team"}
```

**Method 2: Trial and error with descriptive errors**
- JIRA often returns error messages listing valid options when you provide an invalid value

**Method 3: Admin access**
- If you have admin access, check field configuration in JIRA admin panel for allowed values

#### Handling Multi-Project Schemas

Different projects may use different field IDs for similar concepts:

```json
{
  "projects": {
    "PROJ-A": {
      "owning_team_field": "customfield_11077"
    },
    "PROJ-B": {
      "owning_team_field": "customfield_12034"
    }
  }
}
```

**Best Practice**: Store mappings per project, not globally.

### Common Patterns

#### Pattern 1: GXP/Compliance Fields

Regulated industries often have custom fields for compliance:
- Acceptance Criteria (GXP)
- Risk Assessment (GXP)
- Validation Status
- Quality Gate

**Discovery**: Search for keywords like "gxp", "compliance", "validation", "risk", "acceptance"

#### Pattern 2: Agile/Scrum Fields

Agile teams add custom fields:
- Story Points (often `customfield_10060` or `customfield_10016`)
- Sprint (often `customfield_10020`)
- Epic Link (often `customfield_10014`)

**Discovery**: Search for "story", "sprint", "epic"

#### Pattern 3: Team/Ownership Fields

Organizations add team tracking:
- Owning Team / Responsible Team
- Technical Lead
- Product Owner

**Discovery**: Search for "team", "owner", "lead", "responsible"

### Automation Strategy

#### 1. Build a Field Cache

```json
{
  "last_updated": "2025-01-15T10:00:00Z",
  "fields": [
    {
      "id": "customfield_11077",
      "name": "Owning Team",
      "type": "select",
      "projects": ["PROJ-A", "PROJ-B"]
    }
  ]
}
```

**Refresh** the cache periodically (e.g., weekly) or when field discovery fails.

#### 2. Create Field Accessor Functions

```python
def get_field_id(field_name, project_key):
    """Get custom field ID by name and project."""
    cache = load_field_cache()
    for field in cache['fields']:
        if field['name'] == field_name and project_key in field['projects']:
            return field['id']
    # Fallback: search fields API
    return search_and_cache_field(field_name, project_key)
```

#### 3. Validate Before Creation

```python
def validate_custom_fields(project_key, fields):
    """Ensure all custom fields exist and have correct format."""
    for field_name, value in fields.items():
        field_id = get_field_id(field_name, project_key)
        field_type = get_field_type(field_id)
        validate_value_format(value, field_type)
```

### Error Recovery

When field discovery or usage fails:

**Error**: `"Field 'customfield_XXXXX' cannot be set"`
- **Cause**: Field doesn't exist, wrong project, or wrong issue type
- **Solution**: Re-run field search, check project/issue type constraints

**Error**: `"Field value is not valid"`
- **Cause**: Incorrect value format for field type
- **Solution**: Check schema type, verify value format matches expected type

**Error**: `"Field is required"`
- **Cause**: Missing required custom field
- **Solution**: Search for required fields, add to field mappings

### Best Practices Summary

1. **Search First**: Always use `jira_search_fields` before assuming field IDs
2. **Document Mappings**: Store field ID mappings in project configuration files
3. **Test Thoroughly**: Create test issues to verify field IDs and value formats
4. **Cache Strategically**: Build field caches to reduce API calls
5. **Project-Specific**: Don't assume field IDs are the same across projects
6. **Type-Aware**: Respect field types when formatting values
7. **Error-Friendly**: Expect field discovery to fail, have fallback strategies
8. **Version Control**: Check field mappings into version control for team sharing

## Troubleshooting

### Common Issues

**Issue: "Project not found"**
- Verify project key is correct (case-sensitive)
- Use `jira_get_all_projects` to list available projects
- Check JIRA_PROJECTS_FILTER environment variable

**Issue: "Field required but not provided"**
- Use `jira_search_fields` to find required fields
- Check project configuration for mandatory fields
- Some fields may be required by workflow

**Issue: "Invalid transition"**
- Use `jira_get_transitions` to see available transitions
- Check current issue status
- Verify permissions for transition

**Issue: "Custom field not found"**
- Use `jira_search_fields` to find custom field ID
- Format: `customfield_10010`
- Check if field applies to issue type

## Skill Invocation

This skill is automatically invoked when users:
- Ask to "create a JIRA ticket/issue"
- Request "search JIRA for..."
- Say "update JIRA issue..."
- Request "move issue to..." or "transition..."
- Ask about "sprint", "epic", or "board" operations
- Request batch operations on JIRA issues

## Notes

- The Atlassian MCP (`mcp__atlassian`) prefix is used for all tools
- All date/time values use ISO 8601 format
- JQL syntax is similar to SQL but has specific JIRA operators
- Personal space keys in Confluence/JIRA start with `~` and must be quoted
- Cloud vs Server/Data Center may have feature differences
