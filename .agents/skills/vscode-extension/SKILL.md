---
name: vscode-extension
description: Build and package VS Code extensions with viewsContainers, Webview panels, strict CSP, typed postMessage bridges, state persistence, and vsce checks.
when_to_use: "When developing or reviewing VS Code extensions, package.json contributes manifests, Webview UIs, TreeDataProviders, or extension packaging."
allowed-tools: Read, Write, Edit, Glob, Grep, Bash
version: 1.0.0
---

# VS Code Extension — Architecture & Webview Security

> Minimal activation footprint, strict Webview Content Security Policy, and clean extension-host/webview message contracts.

## Core Patterns

1. **Lazy Activation**: Prefer specific `activationEvents` (or rely on VS Code 1.74+ implicit activation from `contributes.commands` / `contributes.views`) instead of `"*"` so editor startup stays fast.
2. **Webview Security (Mandatory)**:
   - Set `localResourceRoots` to only the extension's `media/` or `out/` directory.
   - Include a strict `<meta http-equiv="Content-Security-Policy" ...>` with a per-render cryptographic `nonce` for `<script>` tags (`default-src 'none'; img-src ${webview.cspSource} data:; style-src ${webview.cspSource} 'unsafe-inline'; script-src 'nonce-${nonce}';`).
   - Always resolve local assets with `webview.asWebviewUri(...)`, never raw `file://` paths.
3. **Typed Message Bridge**: Define a discriminated union for `ExtensionToWebviewMessage` and `WebviewToExtensionMessage`; validate incoming webview messages before acting on them in the extension host.
4. **Resource Disposal**: Push every command, listener, and Webview `onDidDispose` handler into `context.subscriptions` to prevent memory leaks on reload/close.
5. **State Persistence**: Use `context.workspaceState` for per-workspace state, `context.globalState` for cross-workspace preferences/high-scores, and `context.secrets` for tokens/credentials.

## Verification Checklist

- `npm run compile` / `tsc --noEmit` succeeds with zero errors.
- `package.json` declares `engines.vscode`, `publisher`, `icon`, `categories`, and valid `contributes` IDs matching `registerWebviewViewProvider` / `registerCommand`.
- `npx @vscode/vsce package` succeeds cleanly without bundling `node_modules` bloat or `.env` files (`.vscodeignore` configured).
