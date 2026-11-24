# Recipe MCP Server (capstone)

## Read me first: pin the MCP SDK to 1.x

**Why:** this course was recorded on the MCP Python SDK **1.x**. A plain `uv add "mcp[cli]"` now
installs **2.x**, where `from mcp.server.fastmcp import FastMCP` fails with `ModuleNotFoundError`
(the class is now `MCPServer`), `FastMCP(host=..., port=...)` moved to `run()`, and the client API
changed. The old lockfile also pinned `mcp` 1.12.4, which has known security flaws, including an
OAuth client that could send credentials to an attacker-chosen authorization server
(GHSA-qx49-fqc8-xw99) and bearer tokens accepted for the wrong resource server (GHSA-w4fh-qvv9-3v23).
Both are fixed in **1.30.0**.

**Install this way to follow the recorded lectures:**

```bash
uv add "mcp[cli]>=1.30,<2"
```

Cloned this repo? Just run `uv sync`. The lockfile already pins `mcp` 1.30.0.
The `legacy-v1` branch stays frozen on this setup: `git checkout legacy-v1`.

**Coming soon:** a new course section on SDK 2.x and the stateless 2026 MCP spec.

## Warning: this server has no authentication

`recipe_server.py` listens on `0.0.0.0` over Streamable HTTP and has **no auth**. Once it's
deployed (Render or anywhere public), anyone who finds the URL can call every tool, including the
ones that write files and report system info. Unauthenticated MCP servers were exploited in the
wild in 2026.

- Treat the deploy lecture as a demo. Tear the service down when you're done, or keep the URL private.
- Don't add tools that touch real data, secrets or paid APIs to a public, unauthenticated server.
- To lock it down on SDK 1.x, use the SDK's resource-server auth: pass `token_verifier=` and
  `auth=AuthSettings(issuer_url=..., resource_server_url=..., validate_token_resource=True)` to
  `FastMCP`, backed by a real OAuth authorization server. See the 1.x authorization docs:
  https://py.sdk.modelcontextprotocol.io/v1/authorization/
- A full auth lecture is part of the new section.

## Run it

```bash
uv sync
uv run recipe_server.py   # Streamable HTTP on $PORT (default 8000), path /mcp
```
