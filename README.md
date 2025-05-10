# Web Search MCP Server

An MCP server for web search and content fetching with DuckDuckGo, with support for multiple URLs and memory optimizations.

## Features

- DuckDuckGo search capability
- Web content fetching from URLs
- Support for fetching multiple URLs in parallel
- Memory management optimizations to prevent lockups
- Rate limiting to avoid API blocks

## Installation

```bash
npm install -g @pinkpixel/web-search-mcp
```

## Usage

### Command Line

```bash
mcp-server-web
```

### With MCP client (Claude Desktop, Cursor, etc.)

Add this to your `config.json`:

```json
{
  "mcpServers": {
    "web-search": {
      "command": "npx",
      "args": [
        "-y",
        "@pinkpixel/web-search-mcp"
      ]
    }
  }
}
```

## Tools

The server provides the following tools:

### search

Search DuckDuckGo and return formatted results.

Parameters:
- `query` (string): The search query string
- `maxResults` (number, optional): Maximum number of results to return (default: 10)

### fetch_content

Fetch and parse content from one or more webpage URLs.

Parameters:
- `url`: Either a single URL string or an array of URL strings

## Development

```bash
# Clone the repository
git clone https://github.com/yourusername/mcp-server-web.git
cd mcp-server-web

# Install dependencies
npm install

# Build
npm run build

# Run
npm start
```

## License

MIT
```
Made with ❤️ by Pink Pixel