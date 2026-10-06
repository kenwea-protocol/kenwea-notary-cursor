---
name: kenwea
description: >-
  Check what an npm package runs at install time before installing it, using the
  Kenwea notary MCP server bundled with this plugin. Use whenever the user is about
  to npm install, pnpm add or yarn add a package they have not used before, asks
  whether a package or tarball is safe to install, or mentions install scripts,
  postinstall, supply chain attacks or Kenwea.
---

# Kenwea notary

The `kenwea-notary` MCP server bundled with this plugin fetches the exact bytes npm would install, runs the package's own install scripts in a container with no network, no capabilities and a read-only filesystem, and returns a verdict signed with Kenwea's published Ed25519 key, bound to the sha256 of what it read. It needs no account and no key.

## Tools

- `kenwea.notary.check`: pass `package` (an npm name, optionally with a version or tag, such as `express@4.18.2`) or `artifactRef` (an https URL to a single .js, .mjs, .cjs or .py file, an npm tarball, or a Python wheel or zip).
- `kenwea.notary.verify`: check a signed record you were given.
- `kenwea.notary.getPublicKey`: get the key, to verify a record in your own code.

## Before installing a package

1. Call `kenwea.notary.check` with the package name and the version you are about to install.
2. Read `verdict` and `verdictReason`:
   - `approved`: the install scripts ran and exited zero. Not an endorsement, and not a statement that the code is good.
   - `rejected`: something in the install surface failed or looked dangerous. Show the user the reason and ask before installing.
   - `manual_review`: nothing ran that could pass. Most often the reason says the package declares no install script, so nothing of its own runs at install time. Otherwise it names a limit on Kenwea's side, such as a missing runtime. Tell the user which one it is; do not call it a failure of the package.
3. Mention the limits when they matter: dependencies are not installed, so the check covers the package's own install scripts and not its dependency tree, and code that runs only when the app calls it is not exercised.

Without a key the server allows 20 checks an hour per network address. If a check is refused for the limit, say so and continue only if the user agrees.

Signed records can be checked independently at https://www.kenwea.com/verify.
