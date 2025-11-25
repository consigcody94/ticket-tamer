# Ticket Tamer

A Model Context Protocol (MCP) server for Jira project management. Create issues, search with JQL, manage epics, add comments, and transition workflows.

## Overview

Ticket Tamer connects your AI assistant to Jira, enabling natural language issue management. Create bugs, update stories, search your backlog, and transition issues through your workflow.

### Why Use Ticket Tamer?

**Traditional workflow:**
- Open Jira in browser
- Navigate through projects
- Fill out issue forms
- Write JQL queries manually

**With Ticket Tamer:**
```
"Create a high-priority bug for the login issue"
"Find all my in-progress issues"
"Move PROJ-123 to Done"
"Add a comment to the deployment issue"
```

## Features

- **Issue Creation** - Create issues with full field control
- **Issue Updates** - Modify any issue fields
- **JQL Search** - Search issues with Jira Query Language
- **Epic Management** - Create epics for organizing work
- **Comments** - Add comments to issues
- **Workflow Transitions** - Move issues through workflow states

## Installation

```bash
# Clone the repository
git clone https://github.com/consigcody94/ticket-tamer.git
cd ticket-tamer

# Install dependencies
npm install

# Build the project
npm run build
```

## Configuration

### Jira Cloud Setup

1. Log in to [Atlassian](https://id.atlassian.com/manage-profile/security/api-tokens)
2. Click "Create API token"
3. Give it a label and create
4. Copy the token (you won't see it again)

### Jira Server/Data Center Setup

1. Log in to your Jira instance
2. Go to Profile → Personal Access Tokens
3. Create a new token
4. Copy the token

### Configuration Object

Each tool requires a config object with these fields:

| Field | Required | Description |
|-------|----------|-------------|
| `host` | Yes | Jira hostname (e.g., `your-domain.atlassian.net`) |
| `username` | Yes | Your email (Cloud) or username (Server) |
| `password` | Yes | API token (Cloud) or password/PAT (Server) |
| `protocol` | No | `https` (default) or `http` |
| `apiVersion` | No | API version (default: `2`) |
| `strictSSL` | No | SSL verification (default: `true`) |

**Example config:**

```json
{
  "host": "your-domain.atlassian.net",
  "username": "your-email@example.com",
  "password": "your-api-token",
  "protocol": "https",
  "apiVersion": "2",
  "strictSSL": true
}
```

### Claude Desktop Integration

Add to your `claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "ticket-tamer": {
      "command": "node",
      "args": ["/absolute/path/to/ticket-tamer/dist/index.js"]
    }
  }
}
```

**Config file locations:**
- macOS: `~/Library/Application Support/Claude/claude_desktop_config.json`
- Windows: `%APPDATA%\Claude\claude_desktop_config.json`
- Linux: `~/.config/Claude/claude_desktop_config.json`

## Tools Reference

### create_issue

Create a new Jira issue.

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `projectKey` | string | Yes | Project key (e.g., `PROJ`) |
| `summary` | string | Yes | Issue title |
| `description` | string | Yes | Issue description |
| `issueType` | string | Yes | Type: `Bug`, `Task`, `Story`, `Epic`, etc. |
| `config` | object | Yes | Jira connection config |

**Example:**

```json
{
  "projectKey": "PROJ",
  "summary": "Login button unresponsive on mobile",
  "description": "## Steps to Reproduce\n\n1. Open app on mobile\n2. Tap login button\n3. Nothing happens\n\n## Expected\n\nLogin modal should open\n\n## Environment\n\n- iOS 17\n- Safari",
  "issueType": "Bug",
  "config": {
    "host": "your-domain.atlassian.net",
    "username": "your-email@example.com",
    "password": "your-api-token"
  }
}
```

**Response includes:**
- Issue ID and key (e.g., `PROJ-123`)
- Issue URL
- Self link for API access

### update_issue

Update an existing issue.

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `issueKey` | string | Yes | Issue key (e.g., `PROJ-123`) |
| `fields` | object | Yes | Fields to update |
| `config` | object | Yes | Jira connection config |

**Example - Update summary and priority:**

```json
{
  "issueKey": "PROJ-123",
  "fields": {
    "summary": "Updated: Login button unresponsive",
    "priority": { "name": "High" }
  },
  "config": {...}
}
```

**Example - Update description and assignee:**

```json
{
  "issueKey": "PROJ-123",
  "fields": {
    "description": "Updated description with more details",
    "assignee": { "accountId": "user-account-id" }
  },
  "config": {...}
}
```

**Common fields:**
- `summary` - Issue title
- `description` - Issue description
- `priority` - `{ "name": "High" }`, `"Medium"`, `"Low"`
- `assignee` - `{ "accountId": "..." }` or `{ "name": "username" }`
- `labels` - `["label1", "label2"]`
- `components` - `[{ "name": "Frontend" }]`

### search_issues

Search issues using JQL (Jira Query Language).

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `jql` | string | Yes | JQL query string |
| `maxResults` | number | No | Max results (default: 50) |
| `config` | object | Yes | Jira connection config |

**Example:**

```json
{
  "jql": "project = PROJ AND status = 'In Progress' ORDER BY priority DESC",
  "maxResults": 20,
  "config": {...}
}
```

**Response includes:**
- Total matching issues
- Returned count
- For each issue:
  - Key and summary
  - Status and priority
  - Assignee info
  - Created/updated dates

### create_epic

Create an Epic issue.

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `projectKey` | string | Yes | Project key |
| `summary` | string | Yes | Epic title |
| `description` | string | Yes | Epic description |
| `epicName` | string | Yes | Short epic name |
| `config` | object | Yes | Jira connection config |

**Example:**

```json
{
  "projectKey": "PROJ",
  "summary": "User Authentication Overhaul",
  "description": "Modernize authentication system with OAuth2, MFA, and SSO support",
  "epicName": "Auth-2024",
  "config": {...}
}
```

### add_comment

Add a comment to an issue.

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `issueKey` | string | Yes | Issue key |
| `comment` | string | Yes | Comment text |
| `config` | object | Yes | Jira connection config |

**Example:**

```json
{
  "issueKey": "PROJ-123",
  "comment": "Fix has been deployed to staging. Please verify and close if resolved.",
  "config": {...}
}
```

### transition_status

Transition an issue to a different status.

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `issueKey` | string | Yes | Issue key |
| `transitionName` | string | Yes | Target status name |
| `config` | object | Yes | Jira connection config |

**Example:**

```json
{
  "issueKey": "PROJ-123",
  "transitionName": "Done",
  "config": {...}
}
```

**Common transitions:**
- `To Do` → `In Progress` → `In Review` → `Done`
- Names vary by project workflow

## JQL Query Reference

### Basic Queries

| Query | Description |
|-------|-------------|
| `project = PROJ` | All issues in project |
| `assignee = currentUser()` | Assigned to you |
| `status = "In Progress"` | In specific status |
| `type = Bug` | Specific issue type |
| `priority = High` | Specific priority |

### Date Queries

| Query | Description |
|-------|-------------|
| `created >= -7d` | Created in last 7 days |
| `updated >= startOfWeek()` | Updated this week |
| `due <= endOfMonth()` | Due this month |
| `resolved >= -30d` | Resolved in last 30 days |

### Combined Queries

| Query | Description |
|-------|-------------|
| `project = PROJ AND status != Done` | Open issues in project |
| `assignee = currentUser() AND status = "In Progress"` | Your in-progress work |
| `type = Bug AND priority = High AND resolution = Unresolved` | High-priority open bugs |
| `sprint in openSprints()` | Issues in active sprints |
| `"Epic Link" = PROJ-100` | Issues in specific epic |

### Ordering

| Query | Description |
|-------|-------------|
| `ORDER BY priority DESC` | Highest priority first |
| `ORDER BY created DESC` | Newest first |
| `ORDER BY updated ASC` | Oldest updates first |

## Workflow Examples

### Bug Triage

1. **Create the bug:**
   ```
   create_issue with projectKey: "PROJ", summary: "...", issueType: "Bug", ...
   ```

2. **Add details via comment:**
   ```
   add_comment with issueKey: "PROJ-123", comment: "Additional info: ..."
   ```

3. **Assign and start:**
   ```
   update_issue with issueKey: "PROJ-123", fields: {assignee: {...}}
   transition_status with issueKey: "PROJ-123", transitionName: "In Progress"
   ```

### Sprint Planning

1. **Search backlog:**
   ```
   search_issues with jql: "project = PROJ AND sprint is EMPTY ORDER BY priority"
   ```

2. **Create epic for feature:**
   ```
   create_epic with projectKey: "PROJ", summary: "...", epicName: "Q1-Feature"
   ```

3. **Create stories under epic:**
   ```
   create_issue with issueType: "Story", ...
   ```

### Issue Resolution

1. **Find issue:**
   ```
   search_issues with jql: "key = PROJ-123"
   ```

2. **Add resolution comment:**
   ```
   add_comment with issueKey: "PROJ-123", comment: "Fixed in commit abc123"
   ```

3. **Close issue:**
   ```
   transition_status with issueKey: "PROJ-123", transitionName: "Done"
   ```

## Requirements

- Node.js 18 or higher
- Jira Cloud, Server, or Data Center instance
- API token or credentials

## Troubleshooting

### "Authentication failed"

1. Verify username is your email (Cloud) or username (Server)
2. Check API token is valid and not expired
3. Ensure correct host format (no `https://` prefix)

### "Project not found"

1. Verify project key is correct (case-sensitive)
2. Ensure your account has access to the project

### "Transition not available"

1. Check issue's current status
2. Verify transition is allowed from current state
3. Use exact transition name from your workflow

### "Field not found"

1. Field names vary by instance
2. Some fields require specific formats
3. Check your Jira's field configuration

## Security Notes

- Never commit credentials to version control
- API tokens have full account access
- Use service accounts for shared usage
- Rotate tokens periodically
- Consider IP allowlisting for production

## License

MIT License - see [LICENSE](LICENSE) file for details.

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## Author

consigcody94
