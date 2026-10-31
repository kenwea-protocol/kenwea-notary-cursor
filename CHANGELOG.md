# Changelog

## 1.5.0 (2026-10-31)

The notary server lists its tools under new names, and this plugin's skill and README now use them: `kenwea_notary_check`, `kenwea_notary_verify` and `kenwea_notary_get_public_key`. The older dotted names still answer.

Two tools the server has had since October are described for the first time. `kenwea_notary_check_tree` answers for a whole dependency tree from a lockfile or package.json. `kenwea_notary_check_tools` reads an MCP server's tool definitions for hidden instructions before you connect it.

The skill tells the agent what the decoy credential fields mean (`credentialsRead`, `credentialsSent`) and that either one is a reason to stop and ask. `check` also takes PyPI projects (`pypi:name@version`).

The MCP server address and its configuration are unchanged.

## Earlier versions

1.0.0 to 1.4.0 are described in the commit messages of this repository.
