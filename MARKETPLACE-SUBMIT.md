# Cursor / Grok Bot marketplace submit checklist

This repo is the Cursor plugin (manifest, hosted MCP config, agent skills, logo) as well as the registry docs. After it lands on `main` of https://github.com/JackVitick/socialync-mcp, submit it.

## Before you submit

- [ ] Files are on the default branch of a **public** GitHub repo
- [ ] `.cursor-plugin/plugin.json` exists, `name` is `socialync` (lowercase kebab-case)
- [ ] `description` is the agent-facing one-liner (Grok Bot SearchPlugins matches on this)
- [ ] `mcp.json` points at `https://mcp.socialync.io/mcp` with **no secrets**
- [ ] Skills have YAML `name` + `description` (the description must say when to use the skill)
- [ ] Logo is committed at `assets/logo.png` and referenced as a relative path
- [ ] Existing MCP registry files (`server.json`, README connect snippets) still work
- [ ] Tested install locally in the Cursor IDE: add the MCP URL, OAuth opens, tools list shows 22 tools, `list_profiles` works, `create_post_draft` WITHOUT `publish_now`
- [ ] Tested in Grok Bot: Settings, then Plugins, add name Socialync, URL `https://mcp.socialync.io/mcp`. If OAuth never starts or you see "fetch failed", use the Bearer header form from the README with a key from https://www.socialync.io/api-keys (never commit it). Confirm tools/list returns fast: a hung MCP times out ALL Grok Bot plugin discovery.

## Where to submit (do both)

1. **Official Cursor Marketplace (what Grok Bot SearchPlugins uses)**
   Sign into a Cursor account, then open
   https://cursor.com/marketplace/publish
   Paste the public repo URL. Manual review. Cursor has said this can take about **2 weeks**. Every update is re-reviewed.

2. **cursor.directory**
   https://cursor.directory/plugins/new
   Create/submit the plugin there as well.

## If it sits in "pending"

After 2 weeks, email **marketplace-publishing@cursor.com** with: plugin name `socialync`, repo `https://github.com/JackVitick/socialync-mcp`, submission date.
Forum pattern that worked for others: https://forum.cursor.com/t/pending-review-xpoz-plugin-submission-submitted-june-24/165776

## What reviewers expect

From https://cursor.com/docs/reference/plugins

- Valid `.cursor-plugin/plugin.json` or root `plugin.json`
- Unique kebab-case `name`
- Description that explains the plugin's purpose
- Valid skill frontmatter
- Relative paths only (no `..`, no absolute paths)
- README covers usage and auth (OAuth in the browser, Bearer key fallback)
- Plugin tested locally

## After it is listed

Search in Grok Bot / Cursor for "Socialync" or "schedule Instagram". The plugin description and skill `description:` lines are the ranking copy. If agents still miss it, tighten those two fields, don't add more skills.
