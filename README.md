# Ticket Tamer

A production-ready Model Context Protocol (MCP) server for Jira project management.

## Features

- **create_issue** - Create new Jira issues
- **update_issue** - Update existing issues
- **search_issues** - Search issues using JQL
- **create_epic** - Create Epic issues
- **add_comment** - Add comments to issues
- **transition_status** - Transition issue status/workflow

## Installation

```bash
npm install
npm run build
```

## Configuration

### Jira Setup

You need a Jira instance with API access enabled. For cloud instances:

1. Generate an API token from your Atlassian account
2. Use your email as the username
3. Use the API token as the password

### Configuration Format

Tools require a configuration object:

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

## Usage

### Claude Desktop Configuration

Add to your `claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "ticket-tamer": {
      "command": "node",
      "args": ["/path/to/ticket-tamer/dist/index.js"]
    }
  }
}
```

### Tool Examples

#### Create an Issue

```javascript
{
  "projectKey": "PROJ",
  "summary": "Fix login bug",
  "description": "Users cannot log in with Google OAuth",
  "issueType": "Bug",
  "config": {...}
}
```

#### Update an Issue

```javascript
{
  "issueKey": "PROJ-123",
  "fields": {
    "summary": "Updated title",
    "description": "Updated description",
    "priority": { "name": "High" }
  },
  "config": {...}
}
```

#### Search Issues

```javascript
{
  "jql": "project = PROJ AND status = 'In Progress'",
  "maxResults": 20,
  "config": {...}
}
```

#### Create an Epic

```javascript
{
  "projectKey": "PROJ",
  "summary": "Q4 Features",
  "description": "All features for Q4 release",
  "epicName": "Q4-2024",
  "config": {...}
}
```

#### Add Comment

```javascript
{
  "issueKey": "PROJ-123",
  "comment": "This has been deployed to staging",
  "config": {...}
}
```

#### Transition Status

```javascript
{
  "issueKey": "PROJ-123",
  "transitionName": "Done",
  "config": {...}
}
```

## JQL Examples

- `project = PROJ AND assignee = currentUser()`
- `status = "In Progress" AND updated >= -7d`
- `priority = High AND created >= startOfWeek()`
- `type = Bug AND resolution = Unresolved`

## Development

```bash
# Install dependencies
npm install

# Build
npm run build

# Run
npm start
```

## Requirements

- Node.js 18+
- Jira Cloud or Server instance
- API token or credentials

## License

MIT

## Author

consigcody94
