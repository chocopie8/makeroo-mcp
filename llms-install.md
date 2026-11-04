# Installing the Makeroo MCP server

Makeroo is a hosted remote MCP server. Nothing needs to be cloned, built or installed, and no API
key, environment variable or header is required.

- URL: `https://makeroo.app/mcp`
- Transport: Streamable HTTP

## Steps

1. Add a remote server named `makeroo` with the URL above to the client's MCP settings. For a
   client that uses an `mcpServers` file:

   ```json
   {
     "mcpServers": {
       "makeroo": {
         "url": "https://makeroo.app/mcp"
       }
     }
   }
   ```

   Cline: add the same entry to `cline_mcp_settings.json` with `"type": "streamableHttp"`.

2. If the client can only start local (stdio) servers, use the bridge instead:

   ```json
   {
     "mcpServers": {
       "makeroo": {
         "command": "npx",
         "args": ["-y", "mcp-remote", "https://makeroo.app/mcp"]
       }
     }
   }
   ```

3. Check it works: call `list_categories` (no arguments). It returns the category ids a submission
   can use. Then call `search_listings` with no arguments to browse the newest live listings.

To list a product, call `submit_listing` to get a preview, show the maker the preview and the
recipient email, and call `confirm_submission` only after they explicitly confirm. Submit only what
you verified from the product's repo or site.
