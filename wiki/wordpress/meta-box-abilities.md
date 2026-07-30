# Meta Box Abilities — AI Agent Actions via MCP

> Meta Box implements the WordPress Abilities API to let AI agents (Claude, Cursor, etc.) directly create, read, update, and delete posts, taxonomies, field groups, and field values through natural language — via the MCP protocol.

## Overview

Meta Box's Abilities feature bridges WordPress content management and AI agents by:

1. **WordPress Abilities API** — WordPress core API that standardizes site actions an agent can take
2. **MCP Adapter plugin** — The official `@automattic/mcp-wordpress-remote` package translates WordPress abilities into the Model Context Protocol that AI tools understand
3. **Meta Box's Abilities** — Concrete implementations for custom post types, taxonomies, field groups, and custom field values

## Setup (3 steps)

### 1. Install MCP Adapter plugin
- Download from [GitHub](https://github.com/Automattic/mcp-wordpress)
- WordPress → Plugins → Add New → Upload Plugin → Install & Activate

### 2. Generate Application Password
- **Users → Profile** → scroll to **Application Passwords**
- Enter a name (e.g. "MCP Agent"), click **Add New Application Password**
- **Copy the password immediately** — shown only once

> ⚠️ An application password grants the same capabilities as the user account it belongs to. For restricted permissions, create a dedicated WordPress user with an Editor or Author role first, then generate an app password under that user.

### 3. Configure MCP Client
Add to your MCP client config (Claude Desktop, Claude Code, Cursor, etc.):

```json
{
  "mcpServers": {
    "wordpress-http": {
      "command": "npx",
      "args": [
        "-y",
        "@automattic/mcp-wordpress-remote@latest"
      ],
      "env": {
        "WP_API_URL": "http://your-site.com/wp-json/mcp/mcp-adapter-default-server",
        "LOG_FILE": "/path/to/logs/mcp-adapter.log",
        "WP_API_USERNAME": "your-username",
        "WP_API_PASSWORD": "your-application-password"
      }
    }
  }
}
```

Replace the domain, username, password, and log path.

## Available Abilities

### 1. Post Type Abilities
*Requires: MB Custom Post Types & Custom Taxonomies (in Meta Box Lite [free] or Meta Box AIO [paid])*

- Create, update, delete **custom post types**
- Get, create, update, delete **posts** within a post type
- Enable per post type under **Features tab → Enable abilities**

Example prompts:
> "Create a custom post type named Event, set the menu icon to 'calendar', enable all abilities."
> "Find 5 latest WordPress events in the world. They're posts of the event post type."
> "Update the status of the posts to published."
> "Delete the events in Asia."

### 2. Taxonomy Abilities
*Requires: same extension as above*

- Create, update, delete **custom taxonomies**
- Get, create, update, delete **terms** within taxonomies

Example prompts:
> "Create a custom taxonomy named 'location' and assign it to the Event custom post type."
> "Enable the Re-order terms feature for this taxonomy."
> "Update locations: remove cities and keep only countries."
> "Delete the locations in Asia."

### 3. Field Group Abilities
*Requires: MB Builder (in Meta Box Lite [free] or Meta Box AIO [paid])*

- Create, update, delete **field groups**
- Add or remove fields
- Update field settings (labels, IDs, types)
- Move fields up/down
- **Enabled by default** — no toggle needed

Example prompts:
> "Create a field group 'Event Details' for the event post type with fields: Address (text) and Date and Time (datetime)."
> "Add a Map field below the Address field, and set the address field ID to 'address'."
> "Move the Date and Time field to the top of the field group."

### 4. Field Value Abilities
*Requires: Meta Box plugin (free — bundled in Lite and AIO)*

- Get, set, edit, delete **custom field values**
- Useful for importing structured data from external sources

Example prompts:
> "Search details for the 5 latest WordPress events you provided, then fill in all the custom fields of those events."
> "Update the date format to Jul 4th, 2026."
> "Delete all data in the custom fields."

## Key Concepts

- **MCP Adapter** exposes a WordPress REST API endpoint at `wp-json/mcp/mcp-adapter-default-server`
- The bridge is two hops: AI tool ↔ `@automattic/mcp-wordpress-remote` (local stdio) ↔ WordPress REST API (HTTP)
- Supports Claude Desktop, Claude Code, Cursor, and any MCP-compatible agent
- Post type and taxonomy abilities require **explicitly enabling** per post type/taxonomy (Features tab → Enable abilities)
- Field group and field value abilities are available by default
- Authentication uses **Application Passwords**, not account passwords

## Connections

- [[wordpress-mcp]] — WordPress MCP ecosystem and tool landscape
- [[meta-box-core]] — Core Meta Box plugin architecture
- [[wordpress-ai-workflows]] — AI agent workflows for WordPress management
- [[mcp]] — Model Context Protocol fundamentals

## Open Questions

- What's the performance like with large numbers of posts/pages?
- Can multiple AI agents connect simultaneously to the same site?
- Does this work with WordPress.com sites or only self-hosted?

## Sources

- [Meta Box Abilities Documentation](https://docs.metabox.io/abilities/)
- [MCP Adapter Plugin (GitHub)](https://github.com/Automattic/mcp-wordpress)

---
tags: [wordpress, meta-box, mcp, ai-agents, api]
last_updated: 2026-07-07