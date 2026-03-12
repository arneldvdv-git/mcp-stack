# MCP Stack - Docker Compose Setup

This docker-compose file sets up a complete MCP (Model Context Protocol) server stack with four essential services for local IDE integration (VS Code, Windsurf, etc.).

## Services Included

1. **Supabase MCP** (Port 3000, 8000, 54321)
   - PostgreSQL database backend
   - Authentication and authorization
   - Real-time capabilities
   - Supabase Studio UI for management

2. **Brave Search MCP** (Port 3001)
   - Web search integration
   - AI-powered search results
   - Requires Brave API key

3. **Crawl4AI MCP** (Port 3002)
   - Web crawling and scraping
   - Content extraction
   - AI-powered data processing

4. **GitHub MCP** (Port 3003)
   - GitHub repository integration
   - Code search and access
   - Issue and PR management
   - Requires GitHub token

## Prerequisites

- Docker and Docker Compose installed
- API Keys for:
  - Brave Search: https://api.search.brave.com/
  - GitHub: https://github.com/settings/tokens (create Personal Access Token)

## Setup Instructions

### 1. Clone/Download this stack
```bash
git clone https://github.com/arneldvdv-git/mcp-stack.git
cd mcp-stack
```

### 2. Configure environment variables
```bash
# Copy the example file
copy .env.example .env

# Edit .env and add your API keys
```

### 3. Start the services
```bash
docker-compose up -d
```

This will start all services in the background. Check status with:
```bash
docker-compose ps
```

### 4. Verify services are running
- Supabase Studio: http://localhost:3000
- Brave Search MCP: http://localhost:3001
- Crawl4AI MCP: http://localhost:3002/health
- GitHub MCP: http://localhost:3003

## Configuring VS Code

Create or update `.vscode/settings.json`:

```json
{
  "[python]": {
    "defaultInterpreterPath": "${workspaceFolder}/venv/bin/python"
  },
  "mcp": {
    "servers": [
      {
        "name": "supabase-mcp",
        "command": "curl",
        "args": ["http://localhost:3000"],
        "type": "http"
      },
      {
        "name": "brave-search-mcp",
        "command": "curl",
        "args": ["http://localhost:3001"],
        "type": "http"
      },
      {
        "name": "crawl4ai-mcp",
        "command": "curl",
        "args": ["http://localhost:3002"],
        "type": "http"
      },
      {
        "name": "github-mcp",
        "command": "curl",
        "args": ["http://localhost:3003"],
        "type": "http"
      }
    ]
  }
}
```

## Configuring Windsurf

Create `.windsurf/settings.json` or update your Windsurf config:

```json
{
  "mcp": {
    "servers": {
      "supabase": {
        "url": "http://localhost:3000"
      },
      "brave-search": {
        "url": "http://localhost:3001"
      },
      "crawl4ai": {
        "url": "http://localhost:3002"
      },
      "github": {
        "url": "http://localhost:3003"
      }
    }
  }
}
```

## Troubleshooting

### Services not starting
```bash
# Check logs for specific service
docker-compose logs brave-search-mcp
docker-compose logs crawl4ai-mcp

# Restart all services
docker-compose restart
```

### Connection refused
- Ensure Docker Desktop is running
- Check firewall settings allow localhost connections
- Verify ports 3000-3003 are not in use

### API Key errors
- Double-check `.env` file has correct API keys
- Ensure keys are not wrapped in quotes in `.env`
- Regenerate tokens if needed

## Useful Commands

```bash
# Start services
docker-compose up -d

# Stop services
docker-compose down

# View logs
docker-compose logs -f

# Rebuild images
docker-compose build

# Remove volumes (WARNING: deletes data)
docker-compose down -v

# Access Supabase database
docker-compose exec supabase-mcp psql -U postgres
```

## Ports Reference

| Service | Port | Purpose |
|---------|------|---------|
| Supabase Studio | 3000 | Web UI |
| Supabase API | 8000 | REST API |
| PostgreSQL | 54321 | Database |
| Brave Search MCP | 3001 | Search API |
| Crawl4AI MCP | 3002 | Crawling API |
| GitHub MCP | 3003 | GitHub API |

## Notes

- All services are connected via `mcp-network` bridge for inter-service communication
- Data persists in Docker volumes (supabase_postgres_data, crawl4ai_data)
- Services have health checks configured
- Environment (.env) should never be committed to version control
