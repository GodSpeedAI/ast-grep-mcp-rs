# ast-grep MCP Server (Rust)

Rust implementation of a [Model Context Protocol (MCP)](https://modelcontextprotocol.io/) server that gives AI assistants structural code search via [ast-grep](https://ast-grep.github.io/).

## Upstream Attribution

This repository is a Rust port of [ast-grep/ast-grep-mcp](https://github.com/ast-grep/ast-grep-mcp).
Original attribution and MIT license notice are preserved in [LICENSE](LICENSE).

## What This Server Provides

The server exposes four MCP tools:

- `dump_syntax_tree`: Inspect syntax tree or pattern structure for debugging rules.
- `test_match_code_rule`: Test a YAML ast-grep rule against code from stdin.
- `find_code`: Search a project with an ast-grep pattern.
- `find_code_by_rule`: Search a project with a full YAML ast-grep rule.

`find_code` and `find_code_by_rule` support:

- `output_format`: `text` (default) or `json`
- `max_results`: optional positive limit for returned matches

## Requirements

1. Install ast-grep CLI (must be available in your `PATH`)

```bash
# Choose one installation method from ast-grep docs
brew install ast-grep
cargo install ast-grep --locked
```

1. Install Rust toolchain

```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

1. Use an MCP-compatible client (Cursor, Claude Desktop, etc.)

## Installation

### Option 1: Install from crates.io (recommended)

```bash
cargo install ast-grep-mcp
```

This installs the executable as:

```text
ast-grep-mcp-server
```

On Linux/macOS, Cargo installs binaries to `~/.cargo/bin` by default.
Make sure that directory is in your `PATH`.

### Option 2: Download a prebuilt binary from GitHub Releases

1. Open the Releases page: <https://github.com/GodSpeedAI/ast-grep-mcp-rs/releases>
2. Download the archive for your platform.
3. Extract it and place `ast-grep-mcp-server` (or `ast-grep-mcp-server.exe` on Windows) somewhere in your `PATH`.
4. Verify checksums with `SHA256SUMS.txt` from the same release.

Release assets are published with names like:

- `ast-grep-mcp-server-vX.Y.Z-x86_64-unknown-linux-gnu.tar.gz`
- `ast-grep-mcp-server-vX.Y.Z-x86_64-apple-darwin.tar.gz`
- `ast-grep-mcp-server-vX.Y.Z-aarch64-apple-darwin.tar.gz`
- `ast-grep-mcp-server-vX.Y.Z-x86_64-pc-windows-msvc.zip`
- `SHA256SUMS.txt`

Checksum verification example:

```bash
sha256sum -c SHA256SUMS.txt
```

## Quick Start (Installed Binary)

1. Install ast-grep CLI and this server.
2. Verify `ast-grep-mcp-server` runs from your shell.
3. Configure your MCP client to launch `ast-grep-mcp-server`.

Quick verification command:

```bash
ast-grep-mcp-server --help
```

## Run

Default transport is stdio:

```bash
ast-grep-mcp-server
```

With custom ast-grep config:

```bash
ast-grep-mcp-server --config /absolute/path/to/sgconfig.yaml
```

You can also set:

```bash
export AST_GREP_CONFIG=/absolute/path/to/sgconfig.yaml
```

## Transport Support

- `stdio`: supported and recommended
- `sse`: currently not implemented in this Rust port

If you run with `--transport sse`, the server exits with an error telling you to use stdio.

## Client Configuration

If you installed via `cargo install ast-grep-mcp` or from GitHub Releases, use command name `ast-grep-mcp-server` directly.

### Cursor

Add to your MCP settings:

```json
{
  "mcpServers": {
    "ast-grep": {
      "command": "ast-grep-mcp-server",
      "args": [],
      "env": {}
    }
  }
}
```

### Claude Desktop

Add to your Claude Desktop MCP config:

```json
{
  "mcpServers": {
    "ast-grep": {
      "command": "ast-grep-mcp-server",
      "args": [],
      "env": {}
    }
  }
}
```

### VS Code

Add to your VS Code MCP configuration:

```json
{
  "mcpServers": {
    "ast-grep": {
      "command": "ast-grep-mcp-server",
      "args": [],
      "env": {}
    }
  }
}
```

Common VS Code user settings locations:

- Linux: `~/.config/Code/User/settings.json`
- macOS: `~/Library/Application Support/Code/User/settings.json`
- Windows: `%APPDATA%\\Code\\User\\settings.json`

## CLI Options

- `--config PATH`: path to `sgconfig.yaml`
- `--transport {stdio|sse}`: default is `stdio` (sse not implemented)
- `--port PORT`: parsed but currently only relevant for future SSE support

## Tool Behavior Notes

- `project_folder` parameters must be absolute paths.
- `test_match_code_rule` returns an error when no matches are found.
- For relational rules (`inside`, `has`), add `stopBy: end` to avoid incomplete traversal.
- Text output is compact (`file:start-end` + matched snippet) to reduce token usage.

## Example Queries for an MCP Client

- Find all Python function definitions in this repo.
- Find Rust `impl` blocks with pattern matching.
- Test this ast-grep YAML rule against a code snippet.
- Dump the CST for this TypeScript snippet.

## Contributing

Contributor and release-maintainer instructions are in [CONTRIBUTING.md](CONTRIBUTING.md).

## Supported Languages

Built-in language set includes:

- bash, c, cpp, csharp, css, elixir, go, haskell, html, java
- javascript, json, jsx, kotlin, lua, nix, php, python, ruby, rust
- scala, solidity, swift, tsx, typescript, yaml

You can extend language support with custom languages in `sgconfig.yaml`.

## Troubleshooting

1. `ast-grep` command not found: install ast-grep and verify it is in `PATH`.
2. No matches for complex relational rules: add `stopBy: end`.
3. Unexpected parse/match behavior: use `dump_syntax_tree` to inspect CST/pattern representation.
4. Config path errors: ensure `--config` or `AST_GREP_CONFIG` points to an existing file.

## License

MIT. See [LICENSE](LICENSE).
