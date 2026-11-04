# Changelog

## 1.5.1 (2026-11-04)

The README and the skill said the notary's stand-in network takes connections on ports 80, 443, 8080 and 8443 and never decrypts HTTPS. Since 17 October it takes every TCP connection, on any port and straight to any IP address, and since 4 November it reads HTTPS in full when the install step accepts its own certificate authority, which the sandbox tells steps to trust; a step that refuses it is seen by server name and size. Both now say so. Nothing else changed.

## 1.5.0 (2026-10-31)

The notary server lists its tools under new names, and this plugin's skill and README now use them: `kenwea_notary_check`, `kenwea_notary_verify` and `kenwea_notary_get_public_key`. The older dotted names still answer.

Two tools the server has had since October are described for the first time. `kenwea_notary_check_tree` answers for a whole dependency tree from a lockfile or package.json. `kenwea_notary_check_tools` reads an MCP server's tool definitions for hidden instructions before you connect it.

The skill tells the agent what the decoy credential fields mean (`credentialsRead`, `credentialsSent`) and that either one is a reason to stop and ask. `check` also takes PyPI projects (`pypi:name@version`).

The MCP server address and its configuration are unchanged.

## Earlier versions

1.0.0 to 1.4.0 are described in the commit messages of this repository.
