---
title: Getting Started
nav_exclude: true
search_exclude: true
description: What the AI Hub Legacy Connector does, how its pieces fit together, and what you need before installing.
---

# Getting Started
{: .no_toc }

1. TOC
{:toc}

## The problem it solves

Liferay AI Hub has two sides: **AI Hub** itself (its own Liferay, where chatbots and agents run) and the **cell** (code on the customer's DXP that mints a token for the signed-in user and hosts the front end — the chat widget and the in-editor assistant). AI Hub acts for a user only when the cell gives the chat widget a token for that user, which AI Hub forwards back to the customer's DXP.

The cell, and Click to Chat's AI Hub provider that places the widget on pages, ship only with **DXP 2026.Q1 and later**. On **2024.Q1 to 2025.Q4** there is no cell, so a licensed AI Hub can't be used from those releases — even with an active subscription.

The **AI Hub Legacy Connector** reproduces the cell side on those earlier releases, as one OSGi module plus OAuth client extensions, without changing Liferay's code.

## How the pieces fit

- **AI Hub Chat** adds AI Hub's widget to the bottom of every page, through the same Liferay include Click to Chat uses. See [Chat Widget](chat.html).
- **AI Hub Tokens** answers the widget's token request (`POST /o/ai-hub-cell/v1.0/authorization-tokens`) the way the cell does: an AI Hub access token, AI Hub's URL, and a per-user token this Liferay issues through an OAuth application. See [Configuration](configuration.html).
- **The proxy** (on by default) keeps AI Hub's access token on the server. The browser gets an opaque handle and calls only its own Liferay at `/o/ai-hub-cell-proxy`, which forwards the widget's calls to AI Hub with the real token.
- **The in-editor AI Assistant** is served to the editor from the module and calls AI Hub's agents through the same proxy. See [In-editor Assistant](in-editor-assistant.html).
- **Liferay Search** backs AI Hub's RAG search node; on 2024.Q1/Q2 the module supplies the endpoint AI Hub calls. See [Liferay Search](liferay-search.html).

The connector **deactivates itself** where Liferay's own cell or Click to Chat's AI Hub provider is present, so upgrading to 2026.Q1+ needs no cleanup beyond uninstalling it.

## What you need

- **A licensed Liferay AI Hub** and an AI Hub account you can configure (the service URL, an OAuth client id and secret for this Liferay, and a chatbot).
- **Liferay DXP 2024.Q1 to 2025.Q4**, any of the eight quarterly releases (see [Compatibility](compatibility.html)). An Enterprise Subscription is required, as for any DXP.
- **Server reachability both ways:** the DXP must reach AI Hub (for tokens and proxied calls), and for AI Hub's Liferay Search node AI Hub must reach the DXP back.
- Access to install an OSGi bundle and OAuth client extensions on the DXP (the installer image automates most of this).

## The shortest path

1. Create the `aihub-connector` user and deploy the OAuth client extension ([Installing](installing.html)).
2. Install the module and point it at AI Hub ([Configuration](configuration.html)).
3. Turn on **AI Hub Chat** ([Chat Widget](chat.html)) and, for authoring help, **AI Hub Assistant** ([In-editor Assistant](in-editor-assistant.html)).
