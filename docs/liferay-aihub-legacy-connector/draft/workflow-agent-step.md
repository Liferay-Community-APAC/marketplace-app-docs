---
title: Workflow Agent Step
nav_exclude: true
search_exclude: true
description: Run an AI Hub agent as a Kaleo workflow step, standing in for the 2026.Q1+ AI Hub agent node.
---

# Workflow Agent Step
{: .no_toc }

1. TOC
{:toc}

On DXP 2026.Q1 and later, a workflow can run an AI Hub agent through Liferay's **AI Hub agent** node. That node is a new node type that needs changes in Liferay's core, so it can't be added to 2024.Q1–2025.Q4 by a module. The connector does the same job through Kaleo's public extension point for actions.

## Turn it on

**Instance Settings → AI Hub → AI Hub Tokens → Workflow Agent Step** (off by default). It runs on the server with AI Hub's token (no browser, no proxy), and needs AI Hub Tokens configured but not AI Hub Chat.

## Define the step

Add a `<task>` assigned to the `aihub-connector` user with an `onAssignment` action whose **script language is `function#aihub-legacy-agent`** and whose script is the step's settings as JSON:

- `agentDefinitionExternalReferenceCode` (required)
- `timeout` in milliseconds (default 120000, max 900000)
- `transition` (default: the task's default) and `errorTransition` (optional)

See the sample workflow shipped with the connector (`examples/ai-hub-agent-step.workflow.xml`).

{: .warning }
Use the `function#aihub-legacy-agent` script language, **not** `java`. From 2024.Q3, Kaleo refuses `java` and Groovy workflow actions unless System Settings → Script Management allows script content to run — which should stay off.

## What it does

When the workflow transaction commits, the connector calls `POST <AI Hub URL>/o/ai-hub/v1.0/agent-instances` synchronously, with AI Hub's access token and, as `Liferay-AI-Hub-Cell-On-Behalf-Of`, a token for **the user who started the workflow**. The agent's response fields (including `output`) go into the workflow context, and the task is completed as its assignee so the workflow moves on.

On failure, the task takes `errorTransition` (with `aiHubAgentError` in the context); without one it waits with `aihub-connector` for an administrator.

## Upgrading to 2026.Q1 or later

Replace the task and its action with Liferay's `<ai-hub-agent>` node, then uninstall the connector.
