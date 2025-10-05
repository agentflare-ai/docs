# Agentflare Documentation

Official documentation for Agentflare - Protocol-level observability for AI agent tool servers.

## Prerequisites

- Node.js v19 or higher (check with `node --version`)
- npm or pnpm package manager

## Local Development

### 1. Install Mintlify CLI

Install the Mintlify CLI globally:

```bash
npm install -g mint
```

Or with pnpm:

```bash
pnpm add -g mint
```

### 2. Run Development Server

Start the local development server:

```bash
mint dev
```

This will start a local preview at `http://localhost:3000`. The development server features hot-reloading, so any changes you make to the documentation files will be reflected immediately.

### 3. Verify Installation

To ensure you have the latest version of Mintlify:

```bash
mint update
```

## Project Structure

```
/
├── docs.json           # Main configuration file
├── README.md          # This file
├── favicon.ico        # Site favicon
├── style.css          # Custom styles
├── images/            # Image assets
│   ├── af.svg
│   ├── agentflare-logo.svg
│   └── icon-only.svg
├── content/           # Main documentation content
│   ├── introduction.mdx
│   ├── quickstart.mdx
│   ├── installation.mdx
│   ├── features-overview.mdx
│   ├── mcp-proxy.mdx
│   └── ...
├── api-reference/     # API documentation
│   ├── introduction.mdx
│   └── endpoints/
└── platform/          # Platform-specific documentation
    ├── platform-overview.md
    ├── websocket-events.md
    └── websocket-protocol.md
```

## Making Changes

### File Types
- **`.mdx` files**: Documentation pages with support for React components
- **`.md` files**: Standard markdown documentation
- **`docs.json`**: Core configuration controlling navigation, styling, and site settings

### Navigation
To modify the documentation structure or navigation, edit the `navigation` field in `docs.json`. The navigation uses a unified recursive structure that combines tabs, groups, and pages.

### Styling
- Custom CSS can be added in `style.css`
- Theme colors and branding are configured in `docs.json`

## Deployment

The documentation automatically deploys when changes are pushed to the main branch. Mintlify handles the build and deployment process.

## Resources

- [Mintlify Documentation](https://mintlify.com/docs)
- [Agentflare Dashboard](https://app.agentflare.com)
- [GitHub Repository](https://github.com/agentflare-ai)

## Support

For questions about the documentation, please open an issue in the GitHub repository or contact the Agentflare team.
