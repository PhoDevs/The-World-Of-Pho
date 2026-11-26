# The World of Pho — GitHub Readiness & Refactoring Checklist

> **Purpose**: This checklist details every action needed to prepare **The World of Pho** repository for GitHub submission. It is broken into modular, prioritized tasks with exact file paths and ready-to-use AI prompts so you can tackle them step-by-step with an AI assistant without getting overwhelmed.

---

## Quick Navigation

1. [Phase 1: Git Repository & Security Sanitation](#phase-1-git-repository--security-sanitation) *(Must Do First)*
2. [Phase 2: Root Directory Cleanup & File Relocation](#phase-2-root-directory-cleanup--file-relocation)
3. [Phase 3: Relocating Out-of-Place Scripts](#phase-3-relocating-out-of-place-scripts)
4. [Phase 4: Consolidating Duplicated Logic](#phase-4-consolidating-duplicated-logic)
5. [Phase 5: Gentle Refactor of the God Object (`PlayerData.gd`)](#phase-5-gentle-refactor-of-the-god-object-playerdatagd)
6. [Phase 6: UI Autoload Diet](#phase-6-ui-autoload-diet)
7. [Phase 7: Public GitHub Polish (README, License, Credits)](#phase-7-public-github-polish-readme-license-credits)
8. [Phase 8: Automated Verification Harness](#phase-8-automated-verification-harness)

---

## Phase 1: Git Repository & Security Sanitation

### Why

Currently, `WorldOfPho` does not have its own isolated `.git` repository (it accidentally points to your user home directory). Additionally, sensitive Android signing keys (`release.keystore`), backup export codes, and over **500 MB** of compiled binaries (`.exe`, `.apk`, `.aab`, `.zip`) are sitting unignored in the project folder.

### Checklist

- [x] **1.1 Initialize Standalone Git Repository**:
  - Run `git init` directly inside `/Users/veterst/Code/WorldOfPho`.
  - Set default branch to `main`.
- [x] **1.2 Create Comprehensive `.gitignore`**:
  - Exclude Godot engine caches (`.godot/`).
  - Exclude Android builds & Gradle caches (`android/build/`, `android/.gradle/`).
  - Exclude binaries (`*.apk`, `*.aab`, `*.exe`, `*.zip`, `*.wasm`, `*.pck`, `*.idsig`, `web build/`).
  - Exclude keystores and secrets (`*.keystore`, `*.jks`, `*BACKUP Key*`, `credentials/`).
  - Exclude OS/IDE files (`.DS_Store`, `.idea/`, `*.tmp`).
- [x] **1.3 Protect Secrets**:
  - Move `release.keystore` and `DO NOT DELETE. BACKUP Key.txt` into an ignored `credentials/` directory.

> **AI Prompt to Use**:
>
> ```text
> Please initialize a standalone git repository in the WorldOfPho project root.
> Create a production-ready .gitignore file specifically tailored for Godot 4 and Android exports,
> ensuring .godot/, android/build/, *.apk, *.exe, *.aab, *.zip, *.keystore, credentials/,
> and backup key files are excluded. Move 'release.keystore' and 'DO NOT DELETE. BACKUP Key.txt'
> into a credentials/ directory and ensure git status does not track them.
> ```

---

## Phase 2: Root Directory Cleanup & File Relocation

### Why

Loose planning text files, abandoned file copies, and debug test logs clutter the root directory and make the repository look untidy.

### Checklist

- [x] **2.1 Delete Dead / Temporary Files**:
  - Delete `scripts/player_data - Copy.gd` (abandoned 1,368-line copy from August 2026).
  - Delete `scenes/Main.tscn*.tmp` files (Godot crash/temp dumps).
  - Delete temporary test dumps in `tests/`: `_bk.txt`, `_chk.txt`, `_e.txt`, `_err.txt`, `_hare.txt`, `_imp.txt`, `_imperr.txt`, `_o.txt`.
  - Remove `New folder/` (contains loose `errorWolfBrutalCampaign.png`).
- [x] **2.2 Organize Loose Documentation into `docs/plans/`**:
  - Move `business district plan.txt` $\rightarrow$ `docs/plans/business_district_plan.txt`
  - Move `Guilds Feature_ Implementation Plan.txt` $\rightarrow$ `docs/plans/guilds_feature_plan.txt`
  - Move `Guilds Feature_ Implementation Plan.pdf` $\rightarrow$ `docs/plans/guilds_feature_plan.pdf`
  - Move `PlayerMarketplacePlan.txt` $\rightarrow$ `docs/plans/marketplace_plan.txt`
  - Move `how_to_add_new_tiles.txt` $\rightarrow$ `docs/guides/how_to_add_new_tiles.txt`
- [ ] **2.3 Handle Large Media Assets**:
  - Decide whether `Demonstration.mp4` (51 MB) should be tracked with Git LFS, uploaded to GitHub Releases / YouTube, or added to `.gitignore`.

> **AI Prompt to Use**:
>
> ```text
> Please clean up the project root:
> 1. Delete dead files: 'scripts/player_data - Copy.gd', any 'scenes/Main.tscn*.tmp' files, 'New folder/', and temporary stdout text files in tests/ (_*.txt).
> 2. Create 'docs/plans/' and 'docs/guides/' directories and move loose planning text files from root into them.
> 3. Verify that no broken file references remain.
> ```

---

## Phase 3: Relocating Out-of-Place Scripts

### Why

Screen scripts are inconsistent: 32 screen scripts are in `scripts/screens/`, but 2 were left in feature subfolders. Additionally, `item_key_registry.gd` sits in `marketplace/` despite being the universal item catalog for the whole game.

### Checklist

- [ ] **3.1 Relocate Screen Scripts to `scripts/screens/`**:
  - Move `scripts/deep_mine/deep_mine_screen.gd` $\rightarrow$ `scripts/screens/deep_mine_screen.gd`.
  - Move `scripts/workshop/workshop_screen.gd` $\rightarrow$ `scripts/screens/workshop_screen.gd`.
  - Update scene script paths in `scenes/screens/deep_mine_screen.tscn` and `scenes/screens/workshop_screen.tscn`.
- [ ] **3.2 Group Loose Feature Data Scripts**:
  - Move `scripts/money_gun_data.gd` $\rightarrow$ `scripts/money_gun/money_gun_data.gd`.
  - Move `scripts/mission_board.gd` $\rightarrow$ `scripts/mission_board/mission_board.gd`.
  - Move `scripts/tutorial_data.gd` $\rightarrow$ `scripts/tutorial/tutorial_data.gd`.
  - Update their corresponding autoload / load paths in `project.godot`.
- [ ] **3.3 Move `ItemKeyRegistry` to Core Location**:
  - Move `scripts/marketplace/item_key_registry.gd` $\rightarrow$ `scripts/items/item_key_registry.gd` (or `scripts/core/item_key_registry.gd`).
  - Update references across all consuming scripts.

> **AI Prompt to Use**:
>
> ```text
> Please harmonize script placement:
> 1. Move 'scripts/deep_mine/deep_mine_screen.gd' and 'scripts/workshop/workshop_screen.gd' into 'scripts/screens/', updating the script references in their corresponding .tscn files and in scripts/screens/screen_navigator.gd.
> 2. Move 'scripts/tutorial_data.gd' into 'scripts/tutorial/tutorial_data.gd'.
> 3. Move 'scripts/money_gun_data.gd' to 'scripts/money_gun/' and update project.godot autoloads.
> 4. Move 'scripts/marketplace/item_key_registry.gd' to 'scripts/items/item_key_registry.gd' and update any references.
> Run a check to ensure all scenes and scripts compile without path errors.
> ```

---

## Phase 4: Consolidating Duplicated Logic

### Why

Multiple scripts implement identical duration formatting, timestamp refreshes, and procedural dialogs independently.

### Checklist

- [ ] **4.1 Centralize Duration & Time Formatting**:
  - Extract the duplicate formatting methods (`_format_time`, `_format_duration`, `_format_time_until`, `format_countdown`) into a shared utility: `scripts/utils/format_utils.gd`.
  - Implement static methods:
    - `FormatUtils.format_duration(seconds: float) -> String`
    - `FormatUtils.format_countdown(seconds: float) -> String`
    - `FormatUtils.format_number(amount: Variant) -> String`
    - `FormatUtils.format_coins(amount: Variant) -> String`
  - Replace private implementations in `workshop_screen.gd`, `guild_detail_screen.gd`, `mission_board_screen.gd`, and `horse_racing_data.gd`.
- [ ] **4.2 Standardize Daily Shop Refresh Logic**:
  - Consolidate timestamp check logic shared between `PlayerData._check_shop_pool_refresh()`, `PlayerData._check_tool_shop_refresh()`, `Garden._check_seed_shop_refresh()`, and `Orchard._check_sapling_shop_refresh()`.
- [ ] **4.3 Reusable Quantity Picker UI Component**:
  - Create `scenes/ui/quantity_picker_dialog.tscn` to replace duplicated +/- quantity adjustment dialogs built procedurally in `guild_detail_screen.gd`, `marketplace_screen.gd`, and `workshop_screen.gd`.

> **AI Prompt to Use**:
>
> ```text
> Create a static helper class 'scripts/utils/format_utils.gd' that provides:
> 1. format_duration(seconds: float) -> String (e.g. "02:15" or "1h 24m")
> 2. format_countdown(seconds: float) -> String
> 3. format_number(val: Variant) -> String
> 4. format_coins(val: Variant) -> String
> Replace the duplicate implementations in workshop_screen.gd, guild_detail_screen.gd,
> mission_board_screen.gd, and horse_racing_data.gd with calls to FormatUtils.
> ```

---

## Phase 5: Gentle Refactor of the God Object (`PlayerData.gd`)

### Why

`scripts/player_data.gd` is 5,492 lines long, containing wallet transactions, 15 inventory systems, prestige multipliers, cheat codes, and save serialization. We break this down **without** breaking existing code by using the **Facade Pattern**.

### Checklist

- [ ] **5.1 Extract `PlayerSaveSystem` (`scripts/player/player_save_system.gd`)**:
  - Moves `ConfigFile` disk reading/writing, backup code generation, and corruption checks out of `PlayerData`.
  - Preserves 100% identical section and key names in `user://player_save.cfg`.
- [ ] **5.2 Extract `PlayerEconomy` (`scripts/player/player_economy.gd`)**:
  - Handles `coins`, transactions, wallet signals, and cheat codes.
- [ ] **5.3 Extract `PlayerInventory` (`scripts/player/player_inventory.gd`)**:
  - Handles item dictionary counters, additions, and removals.
- [ ] **5.4 Keep `PlayerData.gd` as the Transparent Public Facade**:
  - `PlayerData` keeps all existing public variables and methods, forwarding them to the internal helpers.
  - Existing scripts calling `PlayerData.coins` or `PlayerData.get_item_count(...)` continue working with zero changes.

> **AI Prompt to Use**:
>
> ```text
> Refactor 'scripts/player_data.gd' using the Facade Pattern:
> 1. Extract file persistence and backup code logic into a helper 'scripts/player/player_save_system.gd'.
> 2. Extract coin transactions and wallet state into 'scripts/player/player_economy.gd'.
> 3. In 'scripts/player_data.gd', instantiate these helpers and preserve all existing public variable names, getters, setters, and method signatures so that existing caller scripts do not break.
> 4. Ensure save/load retains exact ConfigFile key compatibility with existing player save files.
> ```

---

## Phase 6: UI Autoload Diet

### Why

There are **37 Autoload singletons** registered in `project.godot`. Several of them are transient UI overlays (`DiamondRingNotification`, `GameNotification`, `NotificationWatcher`, `EmojiFontFallback`) that do not belong in the engine's global data namespace.

### Checklist

- [ ] **6.1 Demote Notification Overlays to `scenes/Main.tscn`**:
  - Add notification listeners and overlay widgets directly as child nodes inside a CanvasLayer in `scenes/Main.tscn`.
  - Unregister them from `[autoload]` in `project.godot`.
- [ ] **6.2 Group Guild Sub-Systems under `GuildManager`**:
  - `GuildGarden`, `GuildMine`, `GuildStockpile`, `GuildLeaderboard`, `GuildProduction`, and `GuildStore` should be owned and managed by `GuildManager` rather than 7 individual global singletons.

> **AI Prompt to Use**:
>
> ```text
> Audit the autoloads in project.godot:
> 1. Identify transient UI overlays (such as DiamondRingNotification, GameNotification, NotificationWatcher) and move them to be managed under Main.tscn rather than global engine Autoloads.
> 2. Ensure signals connecting UI notifications to game events continue to trigger properly.
> 3. Verify the game launches cleanly without autoload missing errors.
> ```

---

## Phase 7: Public GitHub Polish (README, License, Credits)

### Why

A repository on GitHub needs clear presentation for reviewers, collaborators, and players.

### Checklist

- [ ] **7.1 Create a Comprehensive `README.md`**:
  - **Banner & Overview**: What "The World of Pho" is (strategic board combat + idle town economy).
  - **Key Features**: Battle system, Garden, Mining, Deep Mine, Workshop, Horse Racing, Guilds, Multiplayer.
  - **Screenshots / GIF Previews**.
  - **Prerequisites & Engine Version**: Godot Engine 4.7+ (Forward Plus / Mobile renderers).
  - **Getting Started Guide**: How to clone, open in Godot, and run.
  - **Project Architecture Summary**: Link to `docs/ARCHITECTURE.md`.
- [ ] **7.2 Create `LICENSE`**:
  - Choose and add a license (e.g., MIT, GPLv3, or All Rights Reserved / Proprietary for game assets).
- [ ] **7.3 Create `CREDITS.md` / `ATTRIBUTION.md`**:
  - Document third-party assets (e.g., `NotoColorEmoji.ttf`, CraftPix background art, sound effects).
- [ ] **7.4 Add `docs/CONTRIBUTING.md`**:
  - Short guide on how to add a new screen or piece without breaking existing systems.

> **AI Prompt to Use**:
>
> ```text
> Please draft a professional, well-formatted README.md for 'The World of Pho'.
> Include:
> 1. Game description and core loop (strategic board battles + idle town progression).
> 2. Engine requirements (Godot 4.7+ Forward+/Mobile, Android portrait 420x900).
> 3. Quickstart instructions for opening and running the project in Godot.
> 4. Directory layout overview and link to docs/ARCHITECTURE.md.
> 5. Create a CREDITS.md file listing third-party fonts and background assets.
> ```

---

## Phase 8: Automated Verification Harness

### Why

Before pushing to GitHub, you need confidence that nothing is broken and that test suites run cleanly headlessly.

### Checklist

- [ ] **8.1 Create Headless Test Runner Script**:
  - Create `tests/run_all_tests.gd` that discovers and runs all test scripts in `tests/`, reporting pass/fail status and returning exit code 0 or 1.

- [ ] **8.2 Verify Project Compilation**:
  - Run the test suite via:

    ```bash
    godot --headless --script tests/run_all_tests.gd
    ```

- [ ] **8.3 Verify Save-File Backwards Compatibility**:
  - Run `test_save_backup_codes.gd` to confirm older save strings continue to parse with 100% fidelity.

> **AI Prompt to Use**:
>
> ```text
> Create a test runner script in 'tests/run_all_tests.gd' extending SceneTree.
> It should run all test_*.gd scripts in the tests/ directory headlessly,
> print a clean summary table of passed/failed tests, and exit with code 0 on success or 1 on failure.
> Run the tests and confirm all current tests pass.
> ```

---

## Suggested Execution Order

|    Step    | Focus Area                                                                      | Estimated Time | Risk Level |
| :--------: | :------------------------------------------------------------------------------ | :------------: | :--------: |
| **Step 1** | Phase 1 (Git init, .gitignore, Keystore protection)                             |     5 mins     |    Zero    |
| **Step 2** | Phase 2 (Clean root clutter, delete `.tmp` & dead files, move plans to `docs/`) |     5 mins     |    Zero    |
| **Step 3** | Phase 3 (Relocate screen scripts to `scripts/screens/`)                         |    10 mins     |  Very Low  |
| **Step 4** | Phase 7 (README.md, LICENSE, and CREDITS.md)                                    |    10 mins     |    Zero    |
| **Step 5** | Phase 4 (FormatUtils & time consolidation)                                      |    15 mins     |    Low     |
| **Step 6** | Phase 5 (PlayerData facade refactor)                                            |    20 mins     |    Low     |
| **Step 7** | Phase 6 & Phase 8 (Autoload cleanup & test harness)                             |    15 mins     |    Low     |
