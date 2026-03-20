# Chrome Extension Project Conventions (Manifest V3)

## Project Structure

- `manifest.json` - Extension manifest (Manifest V3)
- `src/background/` - Service Worker; `src/content/` - Content Script
- `src/popup/` - Popup UI; `src/options/` - Options page; `src/shared/` - Shared code
- `assets/` - Icons (16/32/48/128px) and static resources

## Service Worker Conventions

- Service Workers are event-driven and cannot run persistently — never rely on global variables for state
- All state persistence must use `chrome.storage` — in-memory variables are not reliable
- Register event listeners at the top level (`chrome.runtime.onInstalled`, `chrome.alarms`, etc.)
- Use `chrome.declarativeNetRequest` for network request interception — the deprecated `webRequest` API should not be used
- For long-running tasks, use `chrome.alarms` for scheduled wake-ups (minimum interval: 1 minute)

## Content Script Conventions

- Content Scripts run in an isolated JS environment but share the page DOM
- Always check if elements exist before manipulating the DOM — the target page structure may change
- Prevent global CSS pollution: use Shadow DOM or high-specificity selectors with a unique prefix
- Most `chrome.*` APIs cannot be called directly from Content Scripts — delegate to the Service Worker via message passing

## Message Passing

- Use `chrome.runtime.sendMessage` / `onMessage` to communicate between contexts
- For persistent connections, use `chrome.runtime.connect` to establish a port
- Standardize message format: `{ type: string, payload: unknown }`, routing by `type`
- When responding asynchronously, the `onMessage` listener must `return true`

## Storage API Usage

- `chrome.storage.local` - Local storage, 10MB limit (extendable with `unlimitedStorage` permission)
- `chrome.storage.sync` - Cross-device sync, 8KB per item limit, 100KB total limit
- Use `storage.sync` for user settings (synced across devices); use `storage.local` for cached data
- Listen for changes: `chrome.storage.onChanged.addListener((changes, area) => {})`

## Permission Declarations

- Only declare the minimum set of permissions actually used in `manifest.json`
- Prefer `optional_permissions` for on-demand user authorization to reduce installation friction
- `host_permissions` should match target domains precisely — avoid `<all_urls>`
- Prefer `activeTab` over broad host permissions when applicable
- Permission changes trigger automatic extension disabling — plan for upgrade compatibility

## Common Commands

```bash
npm run dev          # Watch mode, auto-rebuild on file changes
npm run build        # Production build, outputs to dist/
```
- Load the extension: open `chrome://extensions`, enable Developer Mode, click "Load unpacked" and select the `dist/` directory

## Development & Debugging

- Use Vite / webpack for bundling with multiple entry points (background, content, popup)
- Debug Service Worker: `chrome://extensions` -> click the "Service Worker" link
- Before publishing, load as unpacked and perform a full end-to-end test

## Testing

- Use Jest + `@testing-library/dom` to test popup and options page UI
- Mock `chrome.*` APIs: use `jest-chrome` or manually mock `chrome.storage`, `chrome.runtime`, etc.
- Extract Content Script logic into pure functions for isolated testing — avoid reliance on a real DOM environment
- Separate Service Worker event handlers from business logic so the logic can be unit-tested independently
