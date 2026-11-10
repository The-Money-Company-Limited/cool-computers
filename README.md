# Cool Computers plugin

Cool Computers puts what your agent makes online at `name.cool.computer`, where people and agents work on it together with comments and drawings: an artifact first, and a VM when the work needs one. This plugin connects Claude, Claude Code, Codex, ChatGPT, Cursor, Gemini CLI and other agents to it.

## What it ships

- **The Cool Computers MCP server**, `https://api.cool.computer/mcp`. It runs on Cool Computers, not on your machine, and signs in with OAuth the first time you use it.
- **The `cool-computers` skill**, which teaches the agent when to publish an artifact, how to work through comments and drawings, and when a site needs a VM. Its references cover publishing, collaborating and VMs.

Nothing is installed or run locally besides these files.

For Claude Code installations from this repository, plugin updates follow the source revision; the manifest does not pin a version. Refresh the plugin and reload it in Claude Code to pick up changed guidance.

## Before you start

You need a Cool Computers account. Sign up at [cool.computer](https://www.cool.computer). Without an account, an agent can still publish with the [`cool` CLI](https://www.cool.computer/install) and give you a link to claim the site.

## Install

Cool Computers is listed in each app's store as Cool Computers, with the id `cool-computers`.

**Claude Code**, from Anthropic's official marketplace:

```text
/plugin install cool-computers@claude-plugins-official
```

Review the plugin and choose Install for you, then run `/mcp` and sign in to Cool Computers.

**Claude** (claude.ai and the desktop app), the connector: open Customize, then Connectors, choose Discover, search for Cool Computers and connect it. In your new chat, open + → Connectors and make sure Cool Computers is on.

**Claude**, the plugin with the skill: Customize → Plugins → Discover, search Cool Computers, select it and choose Add.

**Codex**: in Codex CLI, run `/plugins`, find Cool Computers, install it and start a new session. In the ChatGPT desktop app, open Plugins, install Cool Computers and start a new Codex chat.

**ChatGPT**: open [Plugins](https://chatgpt.com/plugins), find Cool Computers and choose +.

**Cursor**: install it from the [Cursor Marketplace](https://cursor.com/marketplace/cool-computers), or type `/add-plugin cool-computers` in Cursor's agent.

**Grok**: open [Connectors](https://grok.com/connectors), choose New Connector and search for Cool Computers.

**Gemini CLI**, from the [extensions gallery](https://geminicli.com/extensions/browse/):

```sh
gemini extensions install https://github.com/The-Money-Company-Limited/cool-computers
```

Then run `/mcp auth cool-computers` and sign in to Cool Computers.

**Add it by hand**, from this repository's marketplace.

Claude Code, which installs it for you in every project (then run `/mcp` and sign in):

```sh
claude plugin install cool-computers --marketplace The-Money-Company-Limited/cool-computers
```

Codex:

```sh
codex plugin marketplace add The-Money-Company-Limited/cool-computers && codex plugin add cool-computers@cool-computers
```

**Any agent that reads skills** (the skill only)

```sh
npx skills add The-Money-Company-Limited/cool-computers
```

This installs the skill without the MCP server. With the [`cool` CLI](https://www.cool.computer/install) installed, the skill sends the agent to `cool skill`; otherwise connect the server too, as below.

**MCP server only**, without the skill:

```sh
claude mcp add --scope user --transport http cool-computers https://api.cool.computer/mcp
codex mcp add cool-computers --url https://api.cool.computer/mcp
```

In Codex, also add `required = true` under `[mcp_servers.cool-computers]` in `~/.codex/config.toml`, so Codex waits for Cool Computers' tools before it starts a task. Point any other MCP client at `https://api.cool.computer/mcp` (Streamable HTTP, OAuth).

## Check it works

Ask your agent:

```text
List my Cool Computers sites and computers. Don't change anything.
```

It should answer with your list, from `cool_list_computers` with the MCP server or `cool list` with the CLI.

## More for agents

- [llms.txt](https://www.cool.computer/llms.txt): the index of everything an agent can read
- [setup.md](https://www.cool.computer/setup.md): the step-by-step setup, from a first published page to signing up and keeping it
- [agents.md](https://www.cool.computer/agents.md): the agent guide for everything after setup
- [Documentation](https://cool.computer/docs), with a setup page for each agent and chat app under [Connect your agent](https://cool.computer/docs/agents/overview)

## Issues

This repository is published from Cool Computers' main repository on every change, so pull requests here are overwritten. Please [open an issue](https://github.com/The-Money-Company-Limited/cool-computers/issues) instead.

## License

[MIT](LICENSE)
