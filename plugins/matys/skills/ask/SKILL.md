---
name: ask
description: Ask Matys, the AI data analyst, a business question about a connected datasource. Use when the user's question needs figures, trends, breakdowns or records from their company data (revenue, users, orders, retention, pipeline…), or when they mention Matys or Numize.
argument-hint: "[business question]"
---

# Matys: fast, grounded analytics

Use this skill when a user's question requires data from a datasource connected to Matys. Matys is the data analyst; you are its client. Aim for the **fewest grounded round trips**, not the fewest queries at the expense of correctness.

If the user passed a question with the command, it is: $ARGUMENTS

## Start

1. Call `list_datasources` when you do not already have a confirmed datasource ID for the task, or when the available datasources may have changed. Select the datasource that covers the requested business domain; do not guess its ID. Reuse a confirmed ID for related requests.
2. Call `send_message` with `message` and `datasourceId`. Omit `chatId` for a new investigation. Keep the returned `chatId` for closely related follow-ups.
3. Use `get_chat` only when you need prior message history. Do not fetch the entire transcript before every follow-up. Use `list_chats` only to find an earlier investigation the user refers to.
4. Matys returns text, not a chart object. When another agent will consume the answer, ask for compact text or a small table.

If the Matys tools are missing or return an authentication error, tell the user to run `/mcp`, select `matys` and sign in. If `send_message` returns `subscription_lapsed` or `token_balance_exhausted`, stop and tell the user; retrying will not help.

## Compose a complete first request

State the following **when known and relevant**:

- The business question and metric definition.
- The population or entity, date range, date field, timezone, and exclusions.
- The desired grain and breakdowns—for example, one row per month and region.
- The output shape and an appropriate size limit.
- Whether the user explicitly asked to see SQL or supplied SQL to execute.

**Send the business request, not a SQL query you drafted.** Let Matys choose the appropriate tables, joins, filters, and SQL. Do not translate a user's natural-language question into SQL before calling Matys. The only SQL you should pass along is SQL the user explicitly supplied.

If several figures use the same population and timeframe, request them together. Ask for a reconciliation check **when those figures should reconcile**—for example, when subgroups should add up to a total.

If a term such as "active," "revenue," or "enrolled" could have multiple meanings, provide the user's definition if known. Otherwise, ask Matys to check the available evidence and state the definition used. If the choice would materially change the answer and cannot be resolved from that evidence, ask the user to clarify.

Do not invent table names, column names, coded-value meanings, or customer-specific rules.

## Keep the exchange efficient

- Ask for the direct answer, followed by the essential definition, scope, filters, and caveats. Do not request exhaustive schema exploration or every intermediate query unless the task is a data audit.
- Prefer bounded results, such as aggregates or a relevant top N. **Do not impose a limit when the user explicitly requires complete rows.**
- Reuse a tested *business-question* template for a repeated question, changing the entity and dates as needed. Still request fresh data.
- Continue the same chat for a genuine follow-up so its established scope carries forward. Start a new chat for a different investigation rather than carrying an unrelated, long history.
- If Matys reports an error or ambiguity, correct the specific disputed **business assumption** or clarify the requested scope. Do not write replacement SQL for Matys, repeat the same failed request unchanged, or add speculative instructions. If the issue remains unresolved, explain what needs clarification.
- If `send_message` times out, narrow the scope of the request (shorter window, fewer breakdowns) rather than resending it unchanged.
- If two answers conflict, request one focused reconciliation: the common population, differing definitions or filters, a side-by-side count where useful, and the corrected figure. Do not silently choose one.

## Response contract

Unless the user asks for a different format, request:

> Give the direct answer in a compact table or bullets. State the metric definition, date window, material filters, and any uncertainty. Include a reconciliation check when related totals should reconcile. Choose and run the appropriate queries yourself. Do not include exploratory steps or SQL unless the user explicitly requests them.

Matys may use SQL to verify an answer without returning it. **Do not ask for SQL by default.** If the user asks to see SQL but did not provide a query, ask Matys to produce the SQL it used; do not supply your own.

## User-supplied SQL exception

If the user supplies SQL, you may pass **that SQL** to Matys. If the user explicitly requires it to be run unchanged, say so and request the raw result. Do not replace it with a semantically similar query. If it fails, report the failure and ask permission before modifying it.

## Stop condition

Stop when the requested answer, its scope, and material caveats are present. Do not make another Matys call merely to reformat a satisfactory answer.
