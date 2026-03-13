# Contributing

Thanks for contributing to ast-grep-mcp-rs.

## Local Setup

1. Install Rust using rustup.
2. Install ast-grep CLI and ensure it is in PATH.
3. Clone the repository and build:

```bash
git clone https://github.com/GodSpeedAI/ast-grep-mcp-rs.git
cd ast-grep-mcp-rs
cargo build --release
```

Local binary path:

```text
target/release/ast-grep-mcp-server
```

## Running Locally

Run the server with stdio transport:

```bash
cargo run --release
```

Run with a custom ast-grep config:

```bash
cargo run --release -- --config /absolute/path/to/sgconfig.yaml
```

You can also use:

```bash
export AST_GREP_CONFIG=/absolute/path/to/sgconfig.yaml
```

## Tests and Checks

Run tests:

```bash
cargo test
```

Run compile checks:

```bash
cargo check --all-targets
```

Note: integration tests are skipped automatically when ast-grep is not installed.

## MCP Client Config While Developing

If you are testing with a local build, point your client config command to:

```text
/absolute/path/to/ast-grep-mcp-rs/target/release/ast-grep-mcp-server
```

## Release Process

Release automation is defined in .github/workflows/publish-crate.yml and triggers on tags matching v*.*.\*.

Each release workflow run:

1. Builds Linux, macOS, and Windows binaries.
2. Publishes a GitHub Release with binary archives.
3. Generates SHA256SUMS.txt for asset verification.
4. Publishes the crate to crates.io.

Prerequisite (once):

1. Create a crates.io API token.
2. Add it as GitHub repository secret CARGO_REGISTRY_TOKEN.

Release steps:

1. Update version in Cargo.toml.
2. Commit and push main.
3. Tag and push:

```bash
git tag v0.1.0
git push origin v0.1.0
```
