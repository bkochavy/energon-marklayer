# Energon plugin (`example-energon`)

Agent-native publishing for documents, prototypes, and working files. Use the plugin to publish with feedback, read comments, review changes, read as reference, revise at the same URL when permitted, or make an independent copy. People can also upload directly through the host's browser hub without installing this plugin.

Install this plugin from **this Energon repo** (your deployment repository), not from a placeholder host. The Skills CLI works across compatible agents: `npx skills add example-org/energon --skill example-energon -g`. Claude Code and [Agent Plugins](https://agent-plugins.org/) hosts can also add the same marketplace and install `example-energon@example-energon`. Install at user (global) scope unless the human asked for a project-level install.

Private repositories require GitHub read access in the installing client, including for updates. Keep GitHub credentials separate from the Energon API token. If a client cannot install a private marketplace, follow [Private repository access](../../INSTALL.md#private-repository-access) for a local-skill installation or direct API access. Never put credentials in repository URLs or make the repository public to install the plugin.

After the host exists, ask your agent to connect: it reads https://energon.example/auth.md, shows you a link and code, and saves the approved token as the env named in `GET https://energon.example/v1/help`. For CI, create an agent key at https://energon.example/keys and store it under that name.

Humans and agents: [INSTALL.md](../../INSTALL.md). Copy-paste prompts: [README.md](../../README.md).
