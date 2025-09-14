<div align="center">
  <img width="120" height="120" src="/assets/logo.png" alt="GitHub-Poke Bridge Logo">
  <h1><b>GitHub-Poke Bridge</b></h1>
  <p>
    A proactive GitHub webhook-to-Poke notification bridge using <a href="https://github.com/jlowin/fastmcp">FastMCP</a> for real-time repository event forwarding.
  </p>
</div>

[![Deploy to Render](https://render.com/images/deploy-to-render-button.svg)](https://render.com/deploy?repo=https://github.com/aeastr/github-poke-bridge)

## Features

- 🚀 **Proactive notifications** - Delivers real-time GitHub events to Poke (no polling!)
- 📝 **Rich context** - Provides detailed commit diffs, PR info, and issue details
- 🔍 **Code search** - Searches code within your connected repository
- 📄 **File access** - Retrieves and reads specific files from your repo
- ⚙️ **Configurable** - Toggles diff content inclusion via environment variables
- 🌿 **Comprehensive events** - Supports push, PRs, issues, branches, and tags

## Poke Setup

You can connect your deployed MCP server to Poke at [poke.com/settings/connections](https://poke.com/settings/connections).

**Server URL:** `https://your-app.onrender.com/mcp`
**Transport:** Streamable HTTP

To test the MCP connection, ask Poke something like:
`"Tell the subagent to use the 'GitHub-Poke Bridge' integration's 'test_poke_message' tool"`

## Setting Up Notification Preferences

Once your GitHub webhooks are configured and events are flowing to Poke, you should tell Poke about your notification preferences:

**Example conversation with Poke:**
```
"Hey, I've set up GitHub notifications through my MCP server. Here's how I want you to handle them:

- Only notify me about PRs that are opened or merged
- Alert me immediately for any issues labeled 'bug' or 'critical'
- For commits, only tell me about pushes to main branch with more than 5 files changed
- Branch creation/deletion is usually not important unless it's a release branch
- Feel free to batch minor updates and summarize them once per hour

Can you remember these preferences for future GitHub notifications?"
```

This helps Poke learn what's important to you and avoid notification fatigue.

![GitHub-Poke Bridge Example](/assets/example.png)

## Code Search Capabilities

The MCP server includes code search tools that work with your connected repository:

- **`search_code`** - Searches for code patterns, functions, or text within your repository
- **`get_file_content`** - Retrieves the full content of any file in your repository
- **`get_repository_info`** - Gets basic information and stats about your repository

### Example Usage with Poke

Ask Poke questions like:
- "Search for all functions that contain 'webhook' in our repo"
- "Show me the content of the server.py file"
- "Find all files that import 'requests'"
- "Search for TODO comments in Python files"

The search is scoped to your specific repository (configured via environment variables), making it perfect for repo-specific code exploration and assistance.

## Local Development

### Setup

Fork the repo, then run:

```bash
git clone <your-repo-url>
cd mcp-server-template
conda create -n mcp-server python=3.13
conda activate mcp-server
pip install -r requirements.txt
```

### Test

```bash
python src/server.py
# then in another terminal run:
npx @modelcontextprotocol/inspector
```

Open http://localhost:3000 and connect to `http://localhost:8000/mcp` using "Streamable HTTP" transport (NOTE THE `/mcp`!).

## Configuration

### Environment Variables

Set these in your `.env` file (locally) and Render environment variables:

```bash
# Required
POKE_API_KEY=your-poke-api-key-here
POKE_API_URL=https://poke.com/api/v1/inbound-sms/webhook
GITHUB_TOKEN=your-github-personal-access-token

# Repository Configuration (for code search functionality)
GITHUB_REPO_OWNER=your-github-username-or-org
GITHUB_REPO_NAME=your-repository-name

# Optional
INCLUDE_DIFF_CONTENT=true  # Include actual code changes in notifications
GITHUB_WEBHOOK_SECRET=your-secret  # For webhook security (recommended)
```

### GitHub Webhook Setup

1. Go to your repo → Settings → Webhooks → Add webhook
2. **Payload URL:** `https://your-app.onrender.com/webhook/github`
3. **Content type:** `application/json`
4. **Events:** Select individual events:
   - ✅ Pushes
   - ✅ Pull requests
   - ✅ Issues
   - ✅ Branch or tag creation
   - ✅ Branch or tag deletion

## Deployment

### Option 1: One-Click Deploy
Click the "Deploy to Render" button above.

### Option 2: Manual Deployment
1. Fork this repository
2. Connect your GitHub account to Render
3. Create a new Web Service on Render
4. Connect your forked repository
5. Render will automatically detect the `render.yaml` configuration

Your server will be available at `https://your-service-name.onrender.com/mcp` (NOTE THE `/mcp`!)

## Customization

Add more tools by decorating functions with `@mcp.tool`:

```python
@mcp.tool
def calculate(x: float, y: float, operation: str) -> float:
    """Perform basic arithmetic operations."""
    if operation == "add":
        return x + y
    elif operation == "multiply":
        return x * y
    # ...
```
