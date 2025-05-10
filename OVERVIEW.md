# Web Scout MCP Server - Technical Overview

## Project Overview

The Web Scout MCP Server is a Model Context Protocol (MCP) server implementation that provides web search and content extracting capabilities. It allows AI assistants and other MCP clients to search the web using DuckDuckGo and extract content from web pages, enabling them to access up-to-date information from the internet.

## Key Features

- **DuckDuckGo Search**: Performs web searches using DuckDuckGo
- **Web Content Fetching**: Retrieves and extracts content from web pages
- **Multiple URL Support**: Can extract content from multiple URLs in parallel
- **Memory Management**: Implements optimizations to prevent memory issues
- **Rate Limiting**: Prevents API blocks by limiting request frequency

## Architecture

The project is structured as a TypeScript Node.js application that implements the Model Context Protocol (MCP). It uses the `@modelcontextprotocol/sdk` package to handle the MCP server implementation.

### Core Components

1. **MCP Server**: Handles MCP protocol communication with clients
2. **DuckDuckGoSearcher**: Implements web search functionality
3. **WebContentFetcher**: Implements content fetching with memory optimizations
4. **RateLimiter**: Provides rate limiting for external API requests

## Technical Implementation

### MCP Server

The server is implemented using the `@modelcontextprotocol/sdk` package and exposes two main tools:

1. `DuckDuckGoWebSearch`: Performs web searches using DuckDuckGo
2. `UrlContentExtractor`: Fetches and parses content from web pages

The server uses the StdioServerTransport for communication, making it compatible with various MCP clients.

### DuckDuckGoSearcher

This class handles web searches through DuckDuckGo's HTML interface:

- Makes POST requests to DuckDuckGo's HTML endpoint
- Parses HTML responses using cheerio
- Extracts search results (title, link, snippet)
- Formats results for consumption by LLMs
- Implements rate limiting to prevent API blocks

```typescript
async search(query: string, ctx: Context, maxResults: number = 10): Promise<SearchResult[]> {
  // Apply rate limiting
  await this.rateLimiter.acquire();
  
  // Create form data for POST request
  const data = {
    q: query,
    b: "",
    kl: "",
  };
  
  // Make request to DuckDuckGo
  const response = await axios.post(
    DuckDuckGoSearcher.BASE_URL,
    new URLSearchParams(data),
    {
      headers: DuckDuckGoSearcher.HEADERS,
      timeout: 30000
    }
  );
  
  // Parse results using cheerio
  // ...
}
```

### WebContentFetcher

This class handles fetching and parsing content from web pages:

- Extracts content using axios
- Processes HTML using cheerio to extract text
- Implements memory management optimizations
- Supports extracting content from multiple URLs in parallel
- Uses batch processing based on available memory

#### Memory Management

The WebContentFetcher includes sophisticated memory management:

- Monitors system memory usage
- Uses temporary files for large content
- Adjusts batch sizes based on available memory
- Implements cleanup for temporary files
- Forces garbage collection when available

```typescript
private async processHtml(html: string): Promise<string> {
  // Process in memory or offload to temp file based on size
  const memoryStats = await this.getMemoryStats();
  
  if (html.length > this.MAX_IN_MEMORY_SIZE || memoryStats.usagePercentage > 70) {
    // Write to temporary file and process in chunks
    const tempFilePath = path.join(os.tmpdir(), `mcp-fetch-${uuidv4()}.html`);
    this.tempFiles.push(tempFilePath);
    
    await fs.writeFile(tempFilePath, html);
    
    // Process the file in a memory-efficient way
    // ...
  } else {
    // Process in memory
    // ...
  }
}
```

### Rate Limiting

The RateLimiter class provides rate limiting for external API requests:

- Tracks request timestamps
- Enforces maximum requests per minute
- Implements waiting mechanism when rate limit is reached

```typescript
async acquire(): Promise<void> {
  const now = new Date();
  // Remove requests older than 1 minute
  this.requests = this.requests.filter(
    req => now.getTime() - req.getTime() < 60 * 1000
  );

  if (this.requests.length >= this.requestsPerMinute) {
    // Wait until we can make another request
    const oldestRequest = this.requests[0];
    const waitTime = 60 - (now.getTime() - oldestRequest.getTime()) / 1000;
    if (waitTime > 0) {
      await new Promise(resolve => setTimeout(resolve, waitTime * 1000));
    }
  }

  this.requests.push(now);
}
```

## Installation and Usage

### Installation

```bash
npm install -g @pinkpixel/web-scout-mcp
```

### Command Line Usage

```bash
web-scout-mcp
```

### Integration with MCP Clients

Add to your MCP client's `config.json`:

```json
{
  "mcpServers": {
    "web-search": {
      "command": "npx",
      "args": [
        "-y",
        "@pinkpixel/web-scout-mcp"
      ]
    }
  }
}
```

## Tool Usage

### DuckDuckGoWebSearch

Search DuckDuckGo and return formatted results.

Parameters:
- `query` (string): The search query string
- `maxResults` (number, optional): Maximum number of results to return (default: 10)

### UrlContentExtractor

Retrieve and extract content from one or more webpage URLs.

Parameters:
- `url`: Either a single URL string or an array of URL strings

## Development

```bash
# Clone the repository
git clone https://github.com/pinkpixe-dev/web-scout-mcp.git
cd web-scout-mcp

# Install dependencies
npm install

# Build
npm run build

# Run
npm start
```

## License

This project is licensed under the MIT License.

---

*Last updated: May 10, 2025*
