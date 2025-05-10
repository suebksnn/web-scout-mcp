# Web Scout MCP Server

An MCP server for web search using DuckDuckGo and content extraction, with support for multiple URLs and memory optimizations.

## Features

- DuckDuckGo search capability
- Web content extraction from URLs
- Support for extracting content from multiple URLs in parallel
- Smart memory management optimizations to prevent lockups
- Rate limiting to avoid API blocks

## Installation

```bash
npm install -g @pinkpixel/web-scout-mcp
```

## Usage

### Command Line

```bash
web-scout-mcp
```

### With MCP client (Claude Desktop, Cursor, etc.)

Add this to your `config.json`:

```json
{
  "mcpServers": {
    "web-scout": {
      "command": "npx",
      "args": [
        "-y",
        "@pinkpixel/web-scout-mcp"
      ]
    }
  }
}
```

## Tools

The server provides the following tools:

### DuckDuckGoWebSearch

Initiates a web search query using the DuckDuckGo search engine and returns a well-structured list of findings. Input the keywords, question, or topic you want to search for using DuckDuckGo as your query. Input the maximum number of search entries you'd like to receive using maxResults - defaults to 10 if not provided.

Parameters:
- `query` (string): The search query string
- `maxResults` (number, optional): Maximum number of results to return (default: 10)

### UrlContentExtractor

Fetches and extracts content from a given webpage URL. Input the URL of the webpage you want to extract content from as a string using the url parameter. You can also input an array of URLs to fetch content from multiple pages at once.

Parameters:
- `url`: Either a single URL string or an array of URL strings

## Development

```bash
# Clone the repository
git clone https://github.com/pinkpixel-dev/web-scout-mcp.git
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