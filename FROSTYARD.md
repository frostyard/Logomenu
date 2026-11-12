# Frostyard fork of Logomenu

This is Frostyard's fork of [Aryan20/Logomenu](https://github.com/Aryan20/Logomenu),
a GNOME Shell extension that replaces the Activities button with a menu of quick
actions. Snow (via snosi) builds this extension from a pinned commit of this fork.

## Fork point

The shared base with upstream is commit `fd37eb3331e87307c4f44440016af6eefa937d27`
("Merge pull request #114 from Nixie24/main", 2026-02-13) — the merge-base of
Frostyard's `main` and upstream's `main`. Frostyard's divergence did not begin
there, though: `git log origin/main --not upstream/main` lists the fork-only
commits: 22 up to `ae0ea46`; then documentation-only commits from `a3c8baa`
(which adds this document) to `8927224`; then the System Monitor change (see
"Mission Center replaced by GNOME System Monitor" below). Of the
22, ten precede the `39f262e` merge listed below (early menu
additions such as ChairLift, Distroshelf, Warehouse, Intune/Edge/VSCode
options, and a compiled-schema commit), then `39f262e` re-merges the upstream
state at the fork point, followed by 11 more Frostyard-authored commits ending
at `ae0ea469beaa7ce0cdc1fb8be26fc50c13d4fcbf`. The table below lists only the
12 commits from `39f262e` onward; the ten earlier ones are not itemized here
but are part of the fork's total divergence.

## Pinned commit

snosi's `shared/download/image-checksums.json` pins
`892722440c2afd302bfd15d565c58e7d494994d1`, a documentation-only merge.
Its extension code, metadata and schemas are the same as at
`ae0ea469beaa7ce0cdc1fb8be26fc50c13d4fcbf` ("feat: Use symbolic icon by
default"). The System Monitor change below comes after the pin, so the
pinned commit still shows "Mission Center" and launches
`/usr/libexec/missioncenter-helper`. snosi's `check-dependencies` workflow
opens a pin update to the newest commit on `main`; it needs no tag or
release. That pin update must land in the same snosi change as the
`gnome-system-monitor` package, or after it: an image must not show a menu
item whose program is missing. snosi's `shared/snow/scripts/build/logomenu.chroot`
extracts the whole source archive into
`/usr/share/gnome-shell/extensions/logomenu@aryan_k/`, so a new pin also
copies the new documentation files into the image.

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
| `ae0ea46` | feat: Use symbolic icon by default | Flips `symbolic-icon` default to `true` and changes the icon-image default — the last extension change in the pinned commit `8927224`. |

Frostyard additions in summary: a `frostyard.svg`/`frostyard-symbolic.svg`
branded icon, and menu wiring for uBlue-adjacent tooling (Bazaar, Bold Brew,
Ptyxis, Azure VPN, Edge, VSCode helpers) that upstream does not have. ChairLift, Warehouse and Intune menu items were also added by the
fork's earlier (pre-`39f262e`) commits, then ChairLift and Warehouse were
removed again by `626ef31`, and Intune's menu item was removed by the same
commit (its helper function remains as unused dead code). The pinned commit
therefore ships none of ChairLift, Warehouse or Intune as menu items.

## Mission Center replaced by GNOME System Monitor

The `io.missioncenter.MissionCenter` Flatpak no longer works on Snow, so the
menu goes back to upstream's System Monitor item. The item's label is
"System Monitor" again, and the default of `menu-button-system-monitor` is
upstream's `gnome-system-monitor`, not `/usr/libexec/missioncenter-helper`.
Both lines now match upstream, so this change removes divergence; the
`schemas/gschemas.compiled` file is rebuilt to match. The key stays, so a
user who set their own command keeps it. A user who typed
`/usr/libexec/missioncenter-helper` by hand keeps that value and must reset
the key (`gsettings reset org.gnome.shell.extensions.logo-menu
menu-button-system-monitor`) when snosi removes the helper.

## How far upstream has moved

Since the fork point, upstream added 21 further commits the fork lacks,
most recently `0afd438` ("chore(metadata): bump version to 25.2",
2026-09-28). Notably:

- `e2a2034` "metadata: support GNOME 50" and `021e9d7` "version: bump
  version to 25.0", which adds GNOME 51 — the fork's `metadata.json` still
  lists `"shell-version": ["46", "47", "48", "49"]` (`version` 36,
  `version-name` "24.2"), unchanged since the fork point; upstream's is now
  `["49", "50", "51"]` (`version` 45, `version-name` "25.2"). The fork has
  not picked up GNOME Shell 50 or 51 support.
- `9c1872a` "fix(forcequit): port upstream GNOME 50 fixes and guard against
  killing the shell" — a Force Quit fix the fork lacks. The fork hides
  Force Quit by default (`93ed9bb`), but users can turn it on.
- `b8f5bac` "menu click type to open activities not working" and `aab9ae4`
  "fix issue with clickGesture null" — two functional bug fixes the fork
  lacks.
- `46c15fd` "fix ego concern with disable" — an extensions.gnome.org
  compliance fix the fork lacks.
- `be13867`/`6448db6` — a new CachyOS logo added upstream.
- Several translation-only commits (Swedish, Polish, Georgian) and
  version/metadata bumps with no functional content for Frostyard.

No fork commit narrows or removes upstream's existing shell-version support;
the gap is entirely upstream fixes and GNOME 50 and 51 support the fork has
not replayed.

## Why the fork exists

Not recorded anywhere in this repository's history: no design rationale,
issue or discussion is on record. Unknown, not guessed.

## Recommendation

**Rebase later, keep for now.** The fork's value is entirely in its
Frostyard-specific app wiring, branding and default changes (the 22 fork-only
commits through `ae0ea46`, 12 of them itemized above), none of which touch
`metadata.json` or shell-version support, so they are low-risk to replay on
top of a newer upstream commit once someone verifies behavior under GNOME
50 and 51. Switching Snow to unmodified upstream is not recommended now: upstream
has none of the Frostyard menu entries the fork currently ships (Bold Brew,
Bazaar branding, Ptyxis, Azure VPN wiring), so switching
would drop functionality Snow currently ships.

## Repository baseline

The frostyard repository baseline
([ADR-0071](https://github.com/frostyard/core/blob/main/docs/adr/0071-hold-every-code-repository-to-one-baseline.md),
[spec](https://github.com/frostyard/core/blob/main/docs/specs/repository-baseline.md))
exempts this fork from all of its rows, because the fork follows its
upstream. The README standard
([ADR-0072](https://github.com/frostyard/core/blob/main/docs/adr/0072-write-every-readme-to-one-standard.md),
[spec](https://github.com/frostyard/core/blob/main/docs/specs/readme.md))
asks a fork only for 1 line at the top of `README.md` that links this file.
Do not add baseline files here (`AGENTS.md`, `docs/`, `mise.toml`, new
files in `.github/`): they diverge from upstream, and snosi's build extracts
the whole tree into the image's extension directory, so they would ship to
users.

## Keeping this file current

When snosi's pin or this fork's `main` changes, update the "Pinned commit"
section and every other line that names the pinned commit, the last commit
that changes the extension, or a commit count. Check the facts with:

- the `logomenu` entry in snosi's `shared/download/image-checksums.json`;
- `git log --oneline origin/main --not upstream/main` (fork-only commits);
- `git rev-list --count origin/main..upstream/main` (upstream commits the
  fork lacks).
