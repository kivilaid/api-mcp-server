# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

This is a Model Context Protocol (MCP) server that provides access to the Hostinger API. It's written in TypeScript and JavaScript, exposing Hostinger API endpoints as MCP tools for domains, DNS, billing, and VPS management.

## Common Development Commands

- `npm install` - Install dependencies
- `npm start` - Run the server (defaults to HTTP streaming transport on port 8100)
- `npm run build` - Compile TypeScript to JavaScript and prepare distribution
- `npm run start:ts` - Compile TypeScript and run from dist directory

### Running with different transports:
- `node server.js` - HTTP streaming transport (default)
- `node server.js --sse` - Server-Sent Events transport
- `node server.js --host 0.0.0.0 --port 3000` - Custom host/port
- `node server.js --help` - Show help message

## Architecture

### Transport Layer
The server supports two transport modes:
1. **HTTP Streaming Transport** (default) - Uses `StreamableHTTPServerTransport` for bidirectional streaming over HTTP with SSE
2. **SSE Transport** - Legacy transport using `SSEServerTransport` for Server-Sent Events

### Key Files
- `server.ts` / `server.js` - Main server implementation containing:
  - MCP server setup and tool registration
  - Transport handling (HTTP streaming and SSE)
  - API call execution with authentication
  - Express server configuration with CORS support
- `build.js` - Build script that compiles TypeScript and copies necessary files
- `package.json` - Dependencies and scripts
- `tsconfig.json` - TypeScript configuration

### Authentication
The server uses Bearer token authentication. The API token is read from the `APITOKEN` environment variable and sent in the Authorization header for all API requests.

### Tool Generation
Tools are defined in a large `TOOLS` array (2500+ lines) that maps Hostinger API endpoints to MCP tools. Each tool includes:
- Name, description, HTTP method, and path
- Input schema for parameters
- Security requirements

### Error Handling
- API errors are caught and re-thrown with additional context
- 4xx responses are treated as successful (resolved by axios)
- Detailed error logging when `DEBUG=true`

## Environment Variables

- `APITOKEN` - Hostinger API token (required)
- `DEBUG` - Enable debug logging (true/false)
- `API_BASE_URL` - Base URL for API (default: https://developers.hostinger.com)
- `API_HEADERS` - Additional headers in format "key1:value1,key2:value2"

## Development Notes

- The project uses ES modules (`"type": "module"` in package.json)
- Both TypeScript and JavaScript versions are maintained in sync
- CORS is enabled for all endpoints to support browser-based clients
- Session management is implemented for HTTP streaming transport
- The build process creates a `dist` directory with compiled output