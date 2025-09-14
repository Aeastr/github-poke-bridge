<div align="center">
  <img width="120" height="120" src="/assets/logo.png" alt="GitHub-Poke Bridge Logo">
  <h1><b>GitHub-Poke Bridge</b></h1>
  <p>
    A <strong>proof-of-concept</strong> GitHub integration for Poke using <a href="https://github.com/jlowin/fastmcp">FastMCP</a> that combines proactive webhook notifications with interactive MCP tools for repository management.
  </p>
  <p><em>My first MCP server, built in a few hours for the <a href="https://poke.com">🌴 Poke MCP Challenge</a>.</em></p>
</div>

[![Deploy to Render](https://render.com/images/deploy-to-render-button.svg)](https://render.com/deploy?repo=https://github.com/aeastr/github-poke-bridge)

> **⚠️ Important Notes**
> - **Authentication**: This is a proof-of-concept using personal access tokens for simplicity. In production, this should be developed as a proper GitHub App with OAuth authentication and fine-grained permissions.
> - **Proof-of-Concept**: Built quickly for the Poke MCP Challenge to demonstrate both webhook notifications and MCP tool integration with GitHub
> - **API Keys Required**: You'll need API keys for both Poke and GitHub (see setup instructions below)

## 🚀 Quick Start

1. **Deploy**: Click the "Deploy to Render" button above
2. **Configure**: Set environment variables (see [Environment Variables](#environment-variables))
3. **Connect**: Add to Poke at [poke.com/settings/connections](https://poke.com/settings/connections)
4. **Setup Webhooks**: Configure GitHub webhooks (see [GitHub Webhook Setup](#github-webhook-setup))

## Features

- 🚀 **Proactive notifications** - Delivers real-time GitHub events to Poke (no polling!)
- 📝 **Rich context** - Provides detailed commit diffs, PR info, and issue details
- 🔍 **Code search** - Searches code within your connected repository
- 📄 **File access** - Retrieves and reads specific files from your repo
- ⚙️ **Configurable** - Toggles diff content inclusion via environment variables
- 🌿 **Comprehensive events** - Supports push, PRs, issues, branches, and tags

## 📋 Complete Setup Guide

### 1. Prerequisites

Before starting, you'll need:
- A [GitHub](https://github.com) account with repository access
- A [Poke](https://poke.com) account
- A [Render](https://render.com) account (for deployment)

### 2. Get Required API Keys

#### Poke API Key
1. Go to [poke.com](https://poke.com) and log in
2. Navigate to Settings → Advanced → API Keys
3. Create a new API key and copy it

#### GitHub Personal Access Token
1. Go to GitHub Settings → Developer settings → Personal access tokens → Tokens (classic)
2. Click "Generate new token (classic)"
3. Select scopes:
   - `repo` (full repository access)
   - `project` (project access)
   - `read:org` (read organization data)
   - `read:packages` (read package data)
   - `read:user` (user profile information)
   - `write:discussion` (write discussion comments)
4. Copy the generated token

### 3. Deploy the Server

#### Option A: One-Click Deploy (Recommended)
1. Click the "Deploy to Render" button above
2. Connect your GitHub account to Render
3. Fork the repository when prompted
4. Configure environment variables (see next step)

#### Option B: Manual Deployment
1. Fork this repository to your GitHub account
2. Create a new Web Service on [Render](https://render.com)
3. Connect your forked repository
4. Render will automatically detect the `render.yaml` configuration

### 4. Configure Environment Variables

In your Render dashboard, go to Environment and add these variables:

```bash
# Required - Core functionality
POKE_API_KEY=your-poke-api-key-from-step-2
POKE_API_URL=https://poke.com/api/v1/inbound-sms/webhook
GITHUB_TOKEN=your-github-token-from-step-2

# Required - Repository Configuration
GITHUB_REPO_OWNER=your-github-username-or-org-name
GITHUB_REPO_NAME=your-repository-name

# Optional - Enhanced functionality
INCLUDE_DIFF_CONTENT=true
GITHUB_WEBHOOK_SECRET=your-chosen-webhook-secret
```

### 5. Setup GitHub Webhooks

1. Go to your GitHub repository
2. Navigate to Settings → Webhooks → Add webhook
3. Configure the webhook:
   - **Payload URL**: `https://your-render-app-name.onrender.com/webhook/github`
   - **Content type**: `application/json`
   - **Secret**: Use the same value as `GITHUB_WEBHOOK_SECRET` (optional but recommended)
   - **Events**: Select individual events:
     - ✅ Pushes
     - ✅ Pull requests
     - ✅ Issues
     - ✅ Branch or tag creation
     - ✅ Branch or tag deletion
4. Click "Add webhook"

### 6. Connect to Poke

1. Go to [poke.com/settings/connections](https://poke.com/settings/connections)
2. Add a new MCP server:
   - **MCP Name**: `GitHub-Poke Bridge` (or any name you prefer)
   - **Server URL**: `https://your-render-app-name.onrender.com/mcp`
   - **API Key**: Leave blank (this server doesn't require authentication)
3. Test the connection by asking Poke: `"Tell the subagent to use the 'GitHub-Poke Bridge' integration's 'test_poke_message' tool"`

### 7. Configure Notification Preferences

Tell Poke how you want to receive notifications:

```
"Hey, I've set up GitHub notifications through my MCP server. Here's how I want you to handle them:

- Only notify me about PRs that are opened or merged
- Alert me immediately for any issues labeled 'bug' or 'critical'
- For commits, only tell me about pushes to main branch with more than 5 files changed
- Branch creation/deletion is usually not important unless it's a release branch
- Feel free to batch minor updates and summarize them once per hour

Can you remember these preferences for future GitHub notifications?"
```

## 🏗️ Project Structure

```
github-poke-bridge/
├── src/                          # Main application code
│   ├── __init__.py
│   ├── server.py                 # FastMCP server and MCP tools
│   ├── webhook_handlers.py       # GitHub webhook event handlers
│   ├── poke_client.py           # Poke API client
│   ├── github_client.py         # GitHub API client
│   └── security.py              # Webhook signature validation
├── tests/                        # Test files
│   ├── test_poke.py             # Poke client tests
│   ├── test_repo_access.py      # Repository access tests
│   └── test_code_search.py      # Code search functionality tests
├── planning/                     # Project planning documents
│   ├── ideas.md
│   ├── github-webhook-bridge.md
│   └── roadmap.md
├── poke-tests/                   # Poke integration test files
│   └── hello.md
├── assets/                       # Static assets
│   ├── logo.png
│   └── example.png
├── requirements.txt              # Python dependencies
├── render.yaml                   # Render deployment configuration
└── README.md                     # This file
```

## 🛠️ Available Tools & Capabilities

### Webhook Events Supported
| Event Type | Description | Status |
|-----------|-------------|--------|
| Push | Code commits to any branch | ✅ Supported |
| Pull Request | PR opened, closed, merged, etc. | ✅ Supported |
| Issues | Issue created, closed, labeled, etc. | ✅ Supported |
| Branch Creation | New branches created | ✅ Supported |
| Branch Deletion | Branches deleted | ✅ Supported |
| Tag Creation | New tags created | ✅ Supported |
| Tag Deletion | Tags deleted | ✅ Supported |

### MCP Tools Available
| Tool Name | Description | Example Usage |
|-----------|-------------|---------------|
| `greet` | Welcome message functionality | Testing/Demo |
| `test_poke_message` | Send test message to Poke | Connectivity Testing |
| `search_code` | Search code within repository | "Search for functions containing 'webhook'" |
| `get_file_content` | Retrieve specific file contents | "Show me the server.py file" |
| `get_repository_info` | Get repo stats and information | Repository Overview |
| `add_issue_comment` | Comment on GitHub issues | Issue Management |
| `add_pr_comment` | Comment on pull requests | PR Management |
| `close_issue` | Close GitHub issues | Issue Management |
| `add_issue_labels` | Add labels to issues | Issue Organization |
| `assign_issue` | Assign users to issues | Issue Assignment |

### Example Poke Commands

Ask Poke questions like:
- "Search for all functions that contain 'webhook' in our repo"
- "Show me the content of the server.py file"
- "Find all files that import 'requests'"
- "Search for TODO comments in Python files"
- "What's the latest commit on the main branch?"

## 🧪 Local Development

### Setup Development Environment

```bash
# Clone your fork
git clone https://github.com/your-username/github-poke-bridge.git
cd github-poke-bridge

# Create virtual environment
conda create -n github-poke-bridge python=3.13
conda activate github-poke-bridge

# Install dependencies
pip install -r requirements.txt

# Create local environment file
cp .env.example .env  # Edit with your API keys
```

### Run Locally

```bash
# Start the server
python src/server.py

# In another terminal, test with MCP Inspector
npx @modelcontextprotocol/inspector
```

Open http://localhost:3000 and connect to `http://localhost:8000/mcp` using "Streamable HTTP" transport.

### Run Tests

```bash
# Run all tests
python -m pytest tests/

# Run specific test file
python -m pytest tests/test_poke.py -v
```

## 🔧 Customization

### Adding New MCP Tools

Add new tools by decorating functions with `@mcp.tool`:

```python
@mcp.tool
def create_issue(title: str, body: str, labels: list[str] = None) -> dict:
    """Create a new GitHub issue."""
    # Implementation here
    return {"issue_number": 123, "url": "https://github.com/..."}
```

### Adding New Webhook Events

1. Add handler in `webhook_handlers.py`:
```python
async def handle_release_event(payload: dict):
    """Handle release events."""
    # Process the event
    await send_to_poke(message)
```

2. Register in `server.py`:
```python
@app.post("/webhook/github")
async def github_webhook(request: Request):
    # Add new event type
    elif event_type == "release":
        await handle_release_event(payload)
```

## 🚀 Future Enhancements

Built quickly for the challenge, but potential improvements:

- **Authentication**: OAuth instead of personal access tokens
- **GitHub App**: Proper GitHub App with fine-grained permissions
- **Multi-repo**: Support multiple repositories
- **Enhanced logging**: Better error handling and monitoring
- **Rate limiting**: API endpoint protection
- **Caching**: Repository data caching for performance
- **UI Dashboard**: Web interface for configuration

## 📝 Environment Variables Reference

| Variable | Required | Description | Example |
|----------|----------|-------------|---------|
| `POKE_API_KEY` | ✅ | Your Poke API key | `poke_xxxxxxxxxxxxx` |
| `POKE_API_URL` | ✅ | Poke webhook endpoint | `https://poke.com/api/v1/inbound-sms/webhook` |
| `GITHUB_TOKEN` | ✅ | GitHub personal access token | `ghp_xxxxxxxxxxxxx` |
| `GITHUB_REPO_OWNER` | ✅ | Repository owner/organization | `octocat` |
| `GITHUB_REPO_NAME` | ✅ | Repository name | `Hello-World` |
| `INCLUDE_DIFF_CONTENT` | ❌ | Include code diffs in notifications | `true` |
| `GITHUB_WEBHOOK_SECRET` | ❌ | Webhook signature validation | `my-secret-key` |

![GitHub-Poke Bridge Example](/assets/example.png)
