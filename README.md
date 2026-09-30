# Matys plugin for Claude Code

Connects Claude Code to [Matys](https://www.matys.ai), the AI data analyst, and teaches Claude how to ask it good questions.

It ships:

- **The Matys MCP server** — `list_datasources`, `send_message`, `list_chats`, `get_chat`.
- **The `ask` skill** — how to phrase business questions so Matys answers in few, grounded round trips.

## Install

In a Claude Code session:

```
/plugin marketplace add matys-ai/claude-plugin
/plugin install matys@matys
```

Then run `/mcp`, select `matys` and sign in with your Matys account.

## Use

Ask a data question in plain language, for example *"What was our monthly revenue by region this year?"*. Claude picks up the skill on its own. To call it explicitly, run `/matys:ask <question>`.

## Develop

```
claude plugin validate ./plugins/matys
claude --plugin-dir ./plugins/matys
```
