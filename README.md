# Custom Start Pages

A self-contained browser start page. One HTML file, no dependencies, no build step. Host it anywhere and point every browser at the same URL — your shortcuts travel with a JSON config file instead of being tied to a specific device or browser's localStorage.

---

## Hosting

### Netlify (recommended)
1. Go to [app.netlify.com/drop](https://app.netlify.com/drop)
2. Drag `index.html` onto the page
3. Copy the URL Netlify gives you
4. Set that URL as your browser's new tab / home page

To delete the site later: Netlify dashboard → your site → Site settings → General → scroll to the bottom → **Delete this site**. Export your config first.

### GitHub Pages
1. Create a repo (can be private)
2. Add `index.html` to the root
3. Settings → Pages → Source: main branch → Save
4. Your URL will be `https://<username>.github.io/<repo>/index.html`

### Local file
Open `index.html` directly in your browser (`file:///path/to/index.html`). Everything works except localStorage — changes won't persist across sessions. Fine for a quick look; host it somewhere for real use.

---

## Setting as your start page

| Browser | Setting |
|---------|---------|
| Chrome | Settings → On startup → Open a specific page |
| Firefox | Settings → Home → Custom URLs |
| Safari | Settings → General → Homepage |
| Edge | Settings → Start, home, and new tabs |

Paste your hosted URL. To open a specific profile and network mode on load, use the full URL with params (see [URL parameters](#url-parameters) below).

---

## Profiles

Each profile is an independent set of shortcuts with its own network map. All profiles are stored together in localStorage on the current device.

**Switching profiles** — click the profile button (far left of the toolbar) to open the dropdown.

**Creating a profile** — open the dropdown → **New profile**. The new profile starts empty.

**Deleting a profile** — open the dropdown, hover the profile name, click ×. The last profile cannot be deleted.

Profiles are identified internally by a slug derived from their name (e.g. "Home Network" → `home-network`). Renaming a profile doesn't change its slug or break existing bookmarked URLs.

---

## Shortcuts

**Adding** — click the **+  Add** tile at the end of the grid.

**Editing** — click **Edit** in the toolbar, then click any tile to edit its name, URL, or icon.

**Reordering** — in Edit mode, drag a tile onto another to swap positions. The new order saves automatically.

**Deleting** — in Edit mode, click the × that appears in the top-right corner of a tile.

**Click Done** to exit Edit mode.

### Icons
Icons are fetched automatically from each site's favicon. To use a custom icon, paste any image URL into the icon field when adding or editing a tile. If the favicon fails to load, the tile shows the first letter of the name as a fallback.

### No limit
The grid reflows to fill whatever width is available. Add as many shortcuts as you like.

---

## Search

A search bar sits at the top of the page. Type a query and press Enter to search with the active provider (opens in a new tab).

### Switching providers

Click the provider name (e.g. **DuckDuckGo ▾**) to the right of the search input. The dropdown lets you temporarily pick a different provider for your next search — it resets to the default afterward.

### Managing providers

1. Click the provider dropdown → **⚙ Manage providers…**
2. A panel opens where you can:
   - **Add** providers (click "+ Add provider")
   - **Edit** name and URL template for each provider
   - **Set the default** using the radio button on the left
   - **Delete** with the × button
3. URL templates use `%s` as the query placeholder (e.g. `https://www.google.com/search?q=%s`)

### Per-profile

Search providers and the default selection are stored per profile and included in import/export config files.

---

## Settings

Click the **Settings** button (⚙) in the footer toolbar to open the settings panel. Settings are stored **per profile**.

### Options

| Setting | Description |
|---------|-------------|
| Open links in new window | Off by default — tiles open in the same tab. Toggle on to open in a new tab. |
| Animations | Toggle the tile fade-in animation on page load. |
| Theme | Choose System, Light, Dark, or Custom color scheme. |

---

## Themes

Themes are configured in the **Settings** panel. They are stored **per profile** — each profile can have its own look.

### Options

| Theme | Description |
|-------|-------------|
| System | Follows your OS light/dark preference (default) |
| Light | Always light |
| Dark | Always dark |
| Custom | Pick your own 5 colors with native color pickers |

### Custom theme

When **Custom** is selected, 5 color pickers appear in the dropdown:

| Picker | Controls |
|--------|----------|
| BG | Page background |
| Surface | Tile/card backgrounds |
| Text | Primary text |
| Muted | Secondary/dimmed text |
| Accent | Buttons, highlights |

All other colors (borders, shadows, tertiary text) are derived automatically. Changes apply live as you pick.

Custom colors are included in exported config files and travel with the profile on import.

---

## Import / Export

Config files are plain JSON. Keep one somewhere safe (cloud storage, a notes app, a git repo) and import it whenever you set up a new device or browser.

### Export
Open **Settings** → click **Export this profile** to download the current profile as `<profile-name>-config.json`, or **Export all profiles** for everything in one file.

### Import
Open **Settings** → click **Import** and select a JSON file.

- **Single-profile file** — if the profile name matches the current profile, you'll be asked to confirm before overwriting. If the name differs, you'll be asked whether to add it as a new profile or overwrite the current one.
- **All-profiles file** — shows you exactly which profiles will be overwritten and which are new, then merges everything in.
- **Legacy format** (from an older version of this page) — imported into the current profile.

### Config file format

Single profile:
```json
{
  "profile": {
    "name": "Home",
    "theme": "custom",
    "themeColors": { "bg": "#1a1b26", "surface": "#24283b", "text": "#c0caf5", "muted": "#565f89", "accent": "#7aa2f7" },
    "tiles": [
      { "id": 1, "name": "DuckDuckGo", "url": "https://noai.duckduckgo.com" },
      { "id": 2, "name": "NAS", "url": "http://192.168.0.4", "icon": "https://example.com/icon.png" }
    ],
    "netMap": [
      { "id": 1000, "local": "192.168.0.4", "ts": "nas.home" }
    ],
    "searchProviders": [
      { "name": "DuckDuckGo", "url": "https://noai.duckduckgo.com/?q=%s" },
      { "name": "Google Images", "url": "https://www.google.com/search?tbm=isch&q=%s" }
    ],
    "activeSearchIdx": 0
  }
}
```

All profiles:
```json
{
  "version": 2,
  "profiles": {
    "home": { "name": "Home", "theme": "dark", "tiles": [...], "netMap": [...] },
    "work": { "name": "Work", "theme": "custom", "themeColors": {...}, "tiles": [...], "netMap": [...] }
  }
}
```

**Tile fields:**

| Field | Required | Description |
|-------|----------|-------------|
| `id` | yes | Any unique integer. When adding tiles by hand, increment from the highest existing id. |
| `name` | yes | Display label shown beneath the icon. |
| `url` | yes | Full URL including `https://` or `http://`. |
| `icon` | no | URL of a custom icon image (PNG, SVG, ICO, etc). If omitted, the favicon is fetched automatically from the tile's domain. If that also fails, the first letter of the name is shown as a fallback. |

**netMap fields:**

| Field | Required | Description |
|-------|----------|-------------|
| `id` | yes | Any unique integer. |
| `local` | yes | The local IP or hostname (e.g. `192.168.0.4`). |
| `ts` | yes | The Tailscale hostname (e.g. `nas.home`). |

**Profile-level fields:**

| Field | Required | Description |
|-------|----------|-------------|
| `name` | yes | Display name for the profile. |
| `tiles` | yes | Array of tile objects. |
| `netMap` | no | Array of network map pairs. |
| `theme` | no | `"system"`, `"light"`, `"dark"`, or `"custom"`. Defaults to `"system"`. |
| `themeColors` | no | Custom theme colors (only used when `theme` is `"custom"`). Object with keys: `bg`, `surface`, `text`, `muted`, `accent` — all hex color strings. |
| `searchProviders` | no | Array of `{ "name": "...", "url": "...?q=%s" }` objects. Defaults to DuckDuckGo if omitted. |
| `activeSearchIdx` | no | Index (0-based) of the default search provider. Defaults to `0`. |
| `openNewWindow` | no | `true` to open tiles in a new tab, `false` (default) to open in the same tab. |

You can hand-edit these files to bulk-add shortcuts before importing.

---

## Network map (Local / TS)

For services you access at different addresses depending on your network — typically a local IP at home and a Tailscale hostname when away.

### How it works
Each profile has a network map: a list of `local ↔ ts` address pairs. When you click a tile, the URL is rewritten on the fly based on the current mode. The stored tile URLs are never modified.

- **Local mode** (default, pill off) — URLs open as stored
- **TS mode** (pill on) — any local address in a URL is swapped for its Tailscale hostname before opening

### Setting up
1. Click the **▾** on the Local/TS button to open the network map panel
2. Click **Add address pair**
3. Enter the local IP on the left (e.g. `192.168.0.4`) and the Tailscale hostname on the right (e.g. `nas.home`)
4. Repeat for each service
5. Changes save automatically

The panel slides up from the bottom of the page. Click × or click anywhere outside to close it.

### Toggling
Click the **Local / TS** button to switch modes. The label and pill update instantly and the URL param is updated so bookmarks reflect the current state.

---

## URL parameters

Both params can be combined: `index.html?p=home&net=ts`

| Param | Values | Effect |
|-------|--------|--------|
| `?p=` | profile slug | Opens that profile on load. Falls back to the first profile if the slug doesn't exist. |
| `?net=` | `local` or `ts` | Sets the network mode on load. Defaults to `local` if omitted. |

### Example start page URLs

| URL | Opens |
|-----|-------|
| `index.html?p=home&net=local` | Home profile, local IPs |
| `index.html?p=home&net=ts` | Home profile, Tailscale hostnames |
| `index.html?p=work` | Work profile, local IPs (default) |

Set different browsers or devices to different URLs if they're typically on different networks.

---

## localStorage

Profiles are saved to `localStorage` under the key `start_profiles`. This means:

- Changes persist automatically on the same device and browser
- Clearing browser data wipes your profiles — **export before clearing**
- Different browsers on the same device have separate storage
- The page works when opened as a local file, but changes won't persist (localStorage is blocked on `file://` URLs in most browsers)

The JSON format is identical to the export format, so you can always recover by re-importing a saved config.
