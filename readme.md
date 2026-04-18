# FluxShops

**A groundbreaking, fully GUI-driven chest shop plugin for modern Paper servers.**

FluxShops reimagines the chest shop experience from the ground up. Every action - creating a shop, managing stock, setting sales, configuring taxes, blacklisting players, adding co-owners - is handled through polished, intuitive GUIs. No commands to memorize, no configuration files to hand-edit in-game. Just right-click.

Built exclusively for **Paper 26.1+** with modern APIs: `TextDisplay` and `ItemDisplay` entities for holograms, MiniMessage for all text, and fully async database operations.

---

## Features

### Shops
- **Shift+Left-Click** any chest while holding an item to open the creation GUI
- **SELL**, **BUY**, and **BOTH** shop types
- **Virtual stock** - items are stored in a database, not physically inside the chest
- **Lot-based transactions** - sell/buy in configurable lot sizes (e.g. 64x at a time)
- **Admin shops** - unlimited stock, owner-less, great for server stores
- **Shop transfer** - transfer ownership to any player, with a confirmation GUI
- **WorldGuard integration** - custom `fluxshops-create` flag to restrict shop creation per region

### Holograms
- Proximity-based `TextDisplay` + `ItemDisplay` entities - only visible when you're nearby
- Rotating, bobbing item display
- Shows item name, price, owner name, and stock count
- Configurable: show/hide price, owner, split buy/sell onto separate lines
- Admin shops can hide their stock count
- Stale entity cleanup on startup prevents duplicates after unclean shutdowns

### Shop Browser (`/fs list`)
- Paginated server-wide shop browser
- **Sort** by: Newest, Cheapest, Most Stock, A→Z
- **Filter** by owner name (with player head on exact match) or item name
- **Type filter**: All, Buying only, Selling only
- Auto-hides shops with no usable stock after a configurable number of days; restores them automatically when restocked

### Sales
- Set a % discount on any shop, with an optional time limit
- Apply a sale to one shop or all your shops at once
- Sales respect price minimums - they can never push the price below the server minimum
- Live countdown (h/m/s) visible in the shop view GUI
- Sale badge shown on hologram

### Tax System
- Optional server-wide transaction tax on BUY transactions, voided to fight inflation
- Three-tier priority: **per-shop override → per-material rate → global rate**
- Configurable live from `/fs admin` without a reload
- Tax warning shown to players during shop creation so they know the applicable rate up front
- Per-material tax rates managed through a dedicated admin GUI
- Per-shop tax rate set directly from the admin player detail view

### Co-Owners & Revenue Sharing
- Add co-owners to any shop with a configurable revenue share percentage (0–100%)
- Co-owners can: manage stock, set sales, change price, change shop type
- Co-owners cannot: delete the shop or toggle purchase notifications
- Share can be updated per-shop or across all shops that player co-owns
- Total allocated share across all co-owners is validated - cannot exceed 100%
- Co-owners receive low stock notifications for shops they co-own

### Price Limits
- Set per-material minimum and maximum prices - enforced at shop creation and price changes
- Per-unit enforcement prevents bulk pricing bypasses
- Managed through a dedicated admin GUI with live updates - no reload required

### Price History
- Every price or lot-size change is recorded with a timestamp
- Customers can view the full history from the shop view GUI
- Configurable maximum entries per shop

### Player Blacklist
- Blacklist players from a specific shop, or from all your shops at once (owner scope)
- Managed entirely through a GUI

### Notifications & Preferences
- **Purchase notifications**: owners are notified in chat when someone buys from their shop
- Toggle on/off per-player (global preference, applies to all their shops) from `ShopManageGUI`
- Co-owners never receive purchase notifications regardless of their setting
- **Low stock notifications**: notified on join and periodically while online
  - Covers both owned shops and co-owned shops
  - Configurable threshold (in lots) and repeat interval (in hours)

### Admin Overview (`/fs admin`)
- **Stats panel**: total shops, transactions, and revenue across the server (async-loaded)
- **Price limits**: set min/max prices per material, live
- **Per-item tax rates**: set material-specific tax overrides, separate from price limits
- **Player overview**: browse all players with shops; admin shops pinned first with a custom head
  - Left-click any shop to open its manage GUI
  - Right-click any shop to set a per-shop tax rate override

### Storage & Database
- **SQLite** (default) and **MySQL** supported, switchable via config
- HikariCP connection pooling for MySQL
- Live `/fs migrate confirm` command to migrate SQLite data to MySQL
- Automatic schema migrations on startup - no manual SQL required

### Economy
- **Vault** (hard dependency)
- **Treasury** (soft dependency) - auto-detected, preferred over Vault if present

### Localization
- Full **MiniMessage** support (gradients, hover/click events, etc.)
- Legacy `&` color codes and `&#RRGGBB` hex codes also supported - mix freely
- Bundled translations: `en_us` and `cs_cz`
- Add any language by dropping a new `.yml` in `plugins/FluxShops/translations/`
- Switch language live with `/fs language <code>`

### PlaceholderAPI
- Full expansion available - see the [Placeholders wiki page](../../wiki/Placeholders)

---

## Requirements

| Requirement | Version |
|---|---|
| Paper | 1.21+ (API 26.1+) |
| Java | 21+ |
| Vault | Any current release |

**Optional:** PlaceholderAPI, WorldGuard, Treasury

---

## Installation

1. Drop `FluxShops-x.x.x.jar` into your `plugins/` folder
2. Ensure Vault (and a Vault-compatible economy plugin) is installed
3. Start the server - a `config.yml` and default translation files are generated automatically
4. Configure `plugins/FluxShops/config.yml` to your liking
5. Restart or run `/fs reload`

---

## Commands

| Command | Description | Permission |
|---|---|---|
| `/fs list` | Open the shop browser | `fluxshops.shop.list` |
| `/fs admin` | Open the admin overview GUI | `fluxshops.admin` |
| `/fs reload` | Reload config and language files | `fluxshops.reload` |
| `/fs language <code>` | Switch active language | `fluxshops.language` |
| `/fs migrate confirm` | Migrate SQLite → MySQL | `fluxshops.migrate` |
| `/fs help` | Show command list | - |

Aliases: `/fluxshops`, `/shops`  
Additional aliases configurable in `config.yml` under `command-aliases`.

---

## Permissions

| Permission | Description | Default |
|---|---|---|
| `fluxshops.admin` | All admin commands and bypasses | op |
| `fluxshops.reload` | Reload config and language | op |
| `fluxshops.migrate` | SQLite → MySQL migration | op |
| `fluxshops.language` | Switch language file | op |
| `fluxshops.shop.admin` | Toggle admin shop on any shop | op |
| `fluxshops.shop.bypass` | Bypass WorldGuard region restrictions | op |
| `fluxshops.shop.create` | Create a chest shop | true |
| `fluxshops.shop.delete` | Delete own shops | true |
| `fluxshops.shop.list` | Open `/fs list` | op |
| `fluxshops.shop.teleport` | Teleport to shops from the browser | op |
| `fluxshops.shop.transfer.admin` | Transfer any player's shop | op |
| `fluxshops.shops.unlimited` | No shop limit | op |
| `fluxshops.shops.<N>` | Per-player shop limit (e.g. `fluxshops.shops.10`) | - |

---

## Configuration

See [`config.yml`](config.yml) for the full annotated reference. Key sections:

- `storage` - SQLite vs MySQL, connection pool settings
- `shops` - max shops per player, allow BUY shops, teleport cost, page sizes
- `holograms` - enable/disable, view distance, heights, rotation speed, what to show
- `animations` - chest open animation and sound
- `economy` - preferred provider (`auto`, `vault`, `treasury`)
- `worldguard` - enable/disable the `fluxshops-create` flag integration
- `price-history` - enable/disable, max entries per shop
- `low-stock` - enable/disable, threshold in lots, notification interval in hours
- `shop-expiry` - days before an empty shop is auto-hidden from `/fs list`
- `tax` - enable/disable, global rate (overridable per-material and per-shop)
- `command-aliases` - extra aliases beyond `/fs` and `/shops`

---

## Wiki

- [Configuration Reference](https://github.com/DogeTennant/FluxShops/wiki/Configuration)
- [Permissions](https://github.com/DogeTennant/FluxShops/wiki/Permissions)
- [Tax System](https://github.com/DogeTennant/FluxShops/wiki/Tax-System)
- [Co-Owners & Revenue Sharing](https://github.com/DogeTennant/FluxShops/wiki/Co%E2%80%90Owners)
- [Price Limits](https://github.com/DogeTennant/FluxShops/wiki/Price-Limits)
- [Shop Browser & Expiry](https://github.com/DogeTennant/FluxShops/wiki/Shop-Browser)
- [Holograms](https://github.com/DogeTennant/FluxShops/wiki/Holograms)
- [PlaceholderAPI](https://github.com/DogeTennant/FluxShops/wiki/Placeholders)
- [Storage & Migration](https://github.com/DogeTennant/FluxShops/wiki/Storage)
- [Translations](https://github.com/DogeTennant/FluxShops/wiki/Translations)

---

## License

FluxShops is a **commercial plugin**. Redistribution, decompilation, or resale is prohibited.  
© DogeTennant. All rights reserved.
