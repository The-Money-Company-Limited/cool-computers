# Cool Computers plugin

Cool Computers puts what your agent makes online at `name.cool.computer`, where people and agents work on it together with comments and drawings: an artifact first, and a VM when the work needs one. This plugin connects Claude Code, Codex and other agents to it.

## What it ships

- **The Cool Computers MCP server**, `https://api.cool.computer/mcp`. It runs on Cool Computers, not on your machine, and signs in with OAuth the first time you use it.
- **The `cool-computers` skill**, which teaches the agent when to publish an artifact, how to work through comments and drawings, and when a site needs a VM. Its references cover publishing, collaborating and VMs.

Nothing is installed or run locally besides these files.

## Before you start

You need a Cool Computers account. Sign-up is invite-only for now: join the waitlist at [cool.computer](https://www.cool.computer). Without an account, an agent can still publish with the [`cool` CLI](https://www.cool.computer/install) and give you a link to claim the site.

## Install

**Claude Code**

```text
/plugin marketplace add The-Money-Company-Limited/cool-computers
/plugin install cool-computers@cool-computers
```

Then run `/mcp` and sign in to Cool Computers.

**Codex**

```sh
codex plugin marketplace add The-Money-Company-Limited/cool-computers
codex plugin add cool-computers@cool-computers
```

**Any agent that reads skills**

```sh
npx skills add The-Money-Company-Limited/cool-computers
```

**MCP server only**, without the skill:

```sh
claude mcp add --scope user --transport http cool https://api.cool.computer/mcp
codex mcp add cool --url https://api.cool.computer/mcp
```

In Codex, also add `required = true` under the server's `[mcp_servers.<name>]` table in `~/.codex/config.toml`, so Codex waits for Cool Computers' tools before it starts a task. Point any other MCP client at `https://api.cool.computer/mcp` (Streamable HTTP, OAuth).

## Check it works

Ask your agent:

```text
List my Cool Computers sites and computers. Don't change anything.
```

It should call `cool_list_computers` and answer with your list.

## More for agents

- [llms.txt](https://www.cool.computer/llms.txt): the index of everything an agent can read
- [agents.md](https://www.cool.computer/agents.md): the step-by-step runbook, from sign-up to a first published page
- [Documentation](https://cool.computer/docs) and its MCP connection guide, [/use/mcp](https://cool.computer/docs/use/mcp)

## Issues

This repository is published from Cool Computers' main repository on every change, so pull requests here are overwritten. Please [open an issue](https://github.com/The-Money-Company-Limited/cool-computers/issues) instead.

## License

[MIT](LICENSE)
