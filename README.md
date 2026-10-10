# UE-MCP Studio

UE-MCP Studio is the in-editor client for [ue-mcp](https://github.com/db-lyon/ue-mcp). It adds a Studio tab to Unreal Editor with setup, an AI agent, flows, logs and configuration.

This repository holds release builds only. The ue-mcp server and bridge plugin are open source and free; you can use them from any MCP client without Studio.

## Requirements

- Unreal Engine 5.7 or 5.8 on Windows during the beta.
- [Node.js](https://nodejs.org/en/download) 20 or later, installed for the user who runs the editor.
- For chat: your own Anthropic API key, or a UE-MCP Studio subscription. Subscriptions are not open during the beta.

## Install

From Fab: add UE-MCP Studio to your library and install it to your engine from the Epic Games Launcher. Then enable it in Edit > Plugins in each project that should use it.

From a release zip: download `UE_MCP_Studio-<version>-UE<engine>-Win64.zip` from the [releases page](https://github.com/db-lyon/ue-mcp-studio-releases/releases), unpack it into your project's `Plugins/` folder, and open the project.

Open the tab with the Studio button on the level editor toolbar, or run `Studio.Open` in the console. Studio starts its background services with the editor, so setup begins whether or not the tab is open.

## First run

The tab walks through setup. Each step happens once per project or once per machine.

1. **Node.js.** If Studio cannot find Node.js, it shows a download link. Install the LTS version and select Check again.
2. **The bridge plugin.** Studio runs `npx ue-mcp init` to download the prebuilt `UE_MCP_Bridge` plugin for your engine into the project's `Plugins/` folder. No C++ toolchain is needed. If the bridge depends on engine plugins your project has not loaded yet, Studio asks for one editor restart.
3. **The Studio extension.** Studio runs `npx ue-mcp extension install` with this repository's `latest.json`, which downloads the agent and the signed UI into `~/.ue-mcp/extensions/studio`. Studio checks for a newer extension each time it starts.
4. **The ue-mcp daemon.** Studio starts the per-project ue-mcp daemon, a local process bound to `127.0.0.1` with a random token. The tab connects to it.
5. **A model.** Enter an Anthropic API key in the Settings tab. It is stored on your machine and sent only to Anthropic.

## What Studio downloads

| What | From | Where it goes |
| --- | --- | --- |
| `ue-mcp` (MIT) | npm | the npm cache, run through `npx` |
| `UE_MCP_Bridge` plugin (MIT) | [ue-mcp releases](https://github.com/db-lyon/ue-mcp/releases) | `<Project>/Plugins/UE_MCP_Bridge` |
| Studio extension | this repository's releases | `~/.ue-mcp/extensions/studio` |

Each download is checked against a SHA-256 listed in its manifest. The UI bundle is also signed, and the daemon refuses a bundle whose signature does not match.

## Data

The agent sends your prompts and the project context it reads (asset names, Blueprint graphs, C++ source it opens) to the model provider. With your own API key, that is Anthropic under your account's terms. Apart from the model calls, Studio and ue-mcp contact npm and GitHub to check for and download updates, and GitHub when you file feedback. There is no telemetry.

## Logs

- Studio: `<Project>/Saved/Logs/UE_MCP_Studio.log`
- The daemon: `npx ue-mcp daemon status` in the project folder prints its state.

## Uninstall

1. Disable or remove the plugin.
2. Remove `<Project>/Plugins/UE_MCP_Bridge` if no other client uses it.
3. Run `npx ue-mcp extension remove studio`.

## Third-party software

UE-MCP Studio uses Node.js, ue-mcp and the UE_MCP_Bridge plugin, which you download as described above, and the Anthropic API. The extension bundles open-source packages; their licenses are in `THIRD_PARTY_NOTICES.txt` inside the extension zip.

## Issues

File bugs at [ue-mcp issues](https://github.com/db-lyon/ue-mcp/issues) and say you are using Studio.
