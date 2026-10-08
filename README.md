# Kenwea Notary for Cursor

Before your agent installs a package, it can ask a neutral third party what that
package actually does. Kenwea fetches the exact bytes `npm install` would
download, runs the package's own install scripts in a container with no network,
all capabilities dropped and a read-only filesystem, and returns a verdict signed
under a published Ed25519 key and bound to the sha256 of what it read.

No key, no signup, no payment.

## What it adds

One remote MCP server, `https://mcp.kenwea.com/notary/v1`, with three tools:

| Tool | What it does |
| --- | --- |
| `kenwea.notary.check` | Takes an npm package name (`express@4.18.2`, `@types/node`, or just `lodash` for the latest) or an https URL to a single file, npm tarball or Python wheel. Returns what runs at install (`installSteps`), what the steps attempted (`observed`), a verdict with its `reasonCode`, the sha256 of the bytes, and a signed record. |
| `kenwea.notary.verify` | Checks a signed record against the published key, optionally against a sha256 you hold. Runs nothing. |
| `kenwea.notary.getPublicKey` | Returns the published key, so you can verify a record with your own Ed25519 code instead of asking us. |

It also ships one skill, `skills/kenwea/SKILL.md`, that tells the agent to run the check before it installs an npm package it has not used before, and how to read the verdict.

Try asking Cursor: "Before adding left-pad, check it with the Kenwea notary."

## What the verdict means, and does not

- `approved` means every install step ran to completion under those
  constraints and none tried to reach the network. It is not a statement that
  the code is good or safe for your use, and a script written to notice it is
  being watched can stay quiet.
- A failing install step is `manual_review` (`install_step_failed`), not
  `rejected`: dependencies are not installed, so it often fails for want of one.
  `rejected` is only for a single file that ran and failed, or a
  provider-formatted credential.
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
