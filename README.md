# Kenwea Notary for Cursor

Before your agent installs a package, it can ask a neutral third party what that
package actually does. Kenwea fetches the exact bytes `npm install` would
download, runs the package's own install scripts in a container with no network,
all capabilities dropped and a read-only filesystem, and returns a verdict signed
under a published Ed25519 key and bound to the sha256 of what it read.

No key, no signup, no payment.

## What it adds

One remote MCP server, `https://mcp.kenwea.com/notary/v1`, with two tools:

| Tool | What it does |
| --- | --- |
| `kenwea.notary.check` | Takes an npm package name (`express@4.18.2`, `@types/node`, or just `lodash` for the latest) or an https URL to a single file, npm tarball or Python wheel. Returns `approved`, `manual_review` or `rejected`, the sha256 of the bytes, and a signed record. |
| `kenwea.notary.verify` | Checks a signed record against the published key, optionally against a sha256 you hold. Runs nothing. |

Try asking Cursor: "Before adding left-pad, check it with the Kenwea notary."

## What the verdict means, and does not

- `approved` means it ran under those constraints and exited cleanly. It is not
  a statement that the code is good or safe for your use.
- `manual_review` is also what you get when nothing ran, for example a package
  with no install scripts. The notary does not call unrun code approved.
- Dependencies are not installed, so the check covers a package's own install
  surface, not its transitive tree.
- A URL that cannot be fetched returns `checked: false` with the reason and no
  signature, never a verdict.

## Verify without trusting us

The record's `payload` and `signature` verify with any Ed25519 library against
the key at https://www.kenwea.com/.well-known/kenwea-attestation-key, or in your
browser at https://www.kenwea.com/verify. The claim is about the sha256, not the
URL: hash what you hold and compare.

## Data and limits

- Sent to Kenwea: the package name or URL you ask about, and your network
  address, which is used only for rate limiting.
- Kept: a per-address counter for the hour, a log line saying a check happened
  with a 12-character hash of your address, and the web server's standard access
  log. The artifact, its output and the package or URL you asked about are not
  stored.
- Limits: 20 checks per hour per network address, within a shared hourly
  ceiling for all keyless callers. Over the limit you get `rate_limited` with the
  time the window resets.

## Configuration

Nothing to configure. The plugin's `mcp.json` is:

```json
{ "mcpServers": { "kenwea-notary": { "url": "https://mcp.kenwea.com/notary/v1" } } }
```

## Links

- Notary and verifier: https://www.kenwea.com/verify
- Source of the notary server: https://github.com/kenwea-protocol/kenwea
- Official MCP Registry: `com.kenwea.www/notary`

License: MIT.
