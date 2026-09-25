# Frostyard fork of Logomenu

This is Frostyard's fork of [Aryan20/Logomenu](https://github.com/Aryan20/Logomenu),
a GNOME Shell extension that replaces the Activities button with a menu of quick
actions. Snow (via snosi) builds this extension from a pinned commit of this fork.

## Fork point

The shared base with upstream is commit `fd37eb3331e87307c4f44440016af6eefa937d27`
("Merge pull request #114 from Nixie24/main", 2026-02-13) — the merge-base of
Frostyard's `main` and upstream's `main`. Frostyard's divergence did not begin
there, though: `git log origin/main --not upstream/main` shows 22 fork-only
commits in total. Ten precede the `39f262e` merge listed below (early menu
additions such as ChairLift, Distroshelf, Warehouse, Intune/Edge/VSCode
options, and a compiled-schema commit), then `39f262e` re-merges the upstream
state at the fork point, followed by 11 more Frostyard-authored commits ending
at `ae0ea469beaa7ce0cdc1fb8be26fc50c13d4fcbf`. The table below lists only the
12 commits from `39f262e` onward; the ten earlier ones are not itemized here
but are part of the fork's total divergence.

## Pinned commit

snosi's `shared/download/image-checksums.json` pins
`ae0ea469beaa7ce0cdc1fb8be26fc50c13d4fcbf` ("feat: Use symbolic icon by
default"). **This is the fork's current `main` HEAD** — the fork is not
behind its own tip; Snow ships the latest state of this repository.

## Commits Frostyard added from the `39f262e` merge onward

These 12, authored by Kyle Gospodnetich, 2026-02-20 through 2026-02-24 (the
first is the merge bringing the fork point's upstream state back in; ten
earlier fork-only commits predate this merge and are not itemized here — see
"Fork point" above):

| Commit | Summary | What it changes |
|---|---|---|
| `39f262e` | Merge pull request #1 from Aryan20/main | Merges upstream state at the fork point; no functional change of its own. |
| `626ef31` | Clean up Logomenu | Replaces the earlier fork's settings-controlled Intune item (shown unless `hide-intune`, which defaulted to `true`) and `hide-edge` toggle with settings-gated Edge/VSCode/Azure VPN entries (Intune's menu item is removed; its helper function is left as dead code); removes the earlier fork's ChairLift and Warehouse items, adding Mission Center and Ptyxis instead; moves "About My System" to the bottom of the menu; changes icon defaults. |
| `8fcd59e` | chore: Use full name for Code | Cosmetic label change. |
| `93ed9bb` | chore: Disable force quit by default | Flips `hide-forcequit` default to `true` (hidden unless opted in). |
| `37fe3c9` | Fix default for Bazaar, change title, add new options to prefs menu | Renames "Software Center" to "Bazaar" (Frostyard/uBlue branding), fixes its default helper path to `/usr/libexec/bazaar-helper`, adds prefs rows. |
| `fc65244` | Fix backwards check | Changes the `showActivitiesButton` condition guarding the menu's "Activities" item from `if (!showActivitiesButton)` to `if (showActivitiesButton)`. The negated form came from upstream (`f29b132`, unrelated to any fork commit); this fork commit inverts it, named by its author as a fix. |
| `e5c9fe7` | fix: Add missing rows | Adds preferences rows omitted by a prior commit. |
| `6536c6e` | chore: Use full names | Cosmetic label change. |
| `af89eab` | Add option for symbolic icon, add bold brew menu entry | Adds `Resources/frostyard-symbolic.svg` as an icon choice (Frostyard branding); adds a "Bold Brew" menu entry (`menu-button-bold-brew`, default `/usr/bin/bbrew-helper`), a Frostyard/uBlue-specific app. |
| `7a13538` | fix: Correct command for bold brew | Changes the Bold Brew default from `/usr/bin/bbrew-helper` to `/usr/bin/ptyxis -s -T 'Bold Brew' -- /usr/bin/bbrew-helper` (runs it inside a titled Ptyxis terminal tab rather than directly). |
| `a09c142` | chore: Update compiled schemas | Recompiles `schemas/gschemas.compiled`; no source change. |
| `ae0ea46` | feat: Use symbolic icon by default | Flips `symbolic-icon` default to `true` and changes the icon-image default — the pinned/HEAD commit. |

Frostyard additions in summary: a `frostyard.svg`/`frostyard-symbolic.svg`
branded icon, and menu wiring for uBlue-adjacent tooling (Bazaar, Bold Brew,
Mission Center, Ptyxis, Azure VPN, Edge, VSCode helpers) that upstream does
not have. ChairLift, Warehouse and Intune menu items were also added by the
fork's earlier (pre-`39f262e`) commits, then ChairLift and Warehouse were
removed again by `626ef31`, and Intune's menu item was removed by the same
commit (its helper function remains as unused dead code). The pinned commit
therefore ships none of ChairLift, Warehouse or Intune as menu items.

## How far upstream has moved

Since the fork point, upstream added 18 further commits the fork lacks,
most recently `cf988c0` ("readme: update to latest version", 2026-06-27).
Notably:

- `e2a2034` "metadata: support GNOME 50" — the fork's `metadata.json` still
  lists `"shell-version": ["46", "47", "48", "49"]` (`version` 36,
  `version-name` "24.2"), unchanged since the fork point; upstream's is now
  `["49", "50"]` (`version` 43, `version-name` "24.8"). The fork has not
  picked up GNOME Shell 50 support.
- `b8f5bac` "menu click type to open activities not working" and `aab9ae4`
  "fix issue with clickGesture null" — two functional bug fixes the fork
  lacks.
- `46c15fd` "fix ego concern with disable" — an extensions.gnome.org
  compliance fix the fork lacks.
- `be13867`/`6448db6` — a new CachyOS logo added upstream.
- Several translation-only commits (Swedish, Polish, Georgian) and
  version/metadata bumps with no functional content for Frostyard.

No fork commit narrows or removes upstream's existing shell-version support;
the gap is entirely upstream fixes and GNOME 50 support the fork has not
replayed.

## Why the fork exists

Not recorded anywhere in this repository's history: no design rationale,
issue or discussion is on record. Unknown, not guessed.

## Recommendation

**Rebase later, keep for now.** The fork's value is entirely in its
Frostyard-specific app wiring, branding and default changes (the 22 commits
of divergence, 12 of them itemized above), none of which touch
`metadata.json` or shell-version support, so they are low-risk to replay on
top of a newer upstream commit once someone verifies behavior under GNOME
50. Switching Snow to unmodified upstream is not recommended now: upstream
has none of the Frostyard menu entries the fork currently ships (Bold Brew,
Bazaar branding, Mission Center, Ptyxis, Azure VPN wiring), so switching
would drop functionality Snow currently ships.
