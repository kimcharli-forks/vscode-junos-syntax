# Improvement Plan

## 1. Modernize Grammar Format

**Problem:** `syntaxes/Junos.tmLanguage` is XML plist — verbose and hard to read/diff.

**Plan:**
- Convert to `syntaxes/Junos.tmLanguage.json` (JSON format)
- Update `package.json` grammar `"path"` to point to the new file
- Delete the old `.tmLanguage` file

**Benefit:** Easier to read, edit, and review in PRs.

---

## 2. Fix Overly Broad `.conf` File Association

**Problem:** `.conf` conflicts with nginx, Apache, and many other extensions.

**Plan:**
- Remove `.conf` (plain) from `extensions` in `package.json` and `Junos.tmLanguage`
- Keep `.conf.1` through `.conf.49` (these are Junos-specific rollback config filenames)
- Add a dedicated `.junos` extension as the primary association
- Document how users can manually associate `.conf` files via workspace settings

**Benefit:** Reduces conflicts with other language extensions.

---

## 3. Update Engine Target

**Problem:** `"vscode": "^1.5.0"` is 9+ years old.

**Plan:**
- Bump to `"vscode": "^1.75.0"` (aligns with a modern stable baseline)

**Benefit:** Enables use of modern VS Code APIs and extension features if needed.

---

## 4. Fix `language-configuration.json` Comment Style

**Problem:** `language-configuration.json` uses `//` comments, which are non-standard JSON. Tooling outside VS Code may reject the file.

**Plan:**
- Remove all `//` comments from `language-configuration.json`
- Fix the `lineComment` value: Junos uses `#`, not `//`

**Benefit:** Standard JSON compliance; correct comment token for Junos.

---

## 5. Add Missing Interface Types

**Problem:** The interface regex only covers a subset of Junos interface prefixes. Missing types include:
- `et` (100GE), `fte`, `em`, `fxp`, `bme`, `dsc`, `gre`, `ipip`, `lc`, `mt`, `pd`, `pe`, `pp`, `ppe`, `tap`, `ms`, `rsp`, `pimd`, `pime`, `mtun`, `demux`, `fab`, `rlt`, `jsrv`, `lsq`, `dlc`, `es`, `at`

**Plan:**
- Expand the interface name regex pattern to include the missing prefixes

**Benefit:** More accurate highlighting for all Junos platforms (MX, SRX, EX, QFX, PTX).

---

## 6. Add Missing Top-Level Sections

**Problem:** Several Junos top-level configuration hierarchies are not highlighted, including:
- `groups`, `poe`, `mpls`, `lldp`, `network-access`, `unified-access-control`, `jsrc`, `jsrc-partition`

**Plan:**
- Add missing keywords to the major/minor section match patterns in the grammar

**Benefit:** Proper `entity.name.function` scoping for more configuration blocks.

---

## 7. Add `.vscodeignore`

**Problem:** No `.vscodeignore` means the `docs/` folder and other non-runtime files are bundled into the published `.vsix`.

**Plan:**
- Create `.vscodeignore` to exclude:
  - `docs/`
  - `images/` (if not needed at runtime — check if icon is used)
  - `*.md` (except top-level `README.md`)
  - `.git*`

**Benefit:** Smaller published extension package.

---

## 8. Add CI / Basic Testing

**Problem:** No automated tests or CI pipeline.

**Plan:**
- Add a GitHub Actions workflow (`.github/workflows/ci.yml`) that:
  - Lints `package.json` (valid JSON)
  - Validates grammar file parses without errors
  - Runs on push and PRs to `master`
- Optionally add snapshot tests using `vscode-tmgrammar-test`

**Benefit:** Catches regressions when editing the grammar.

---

## Priority Order

| Priority | Item |
|---|---|
| High | 4 — Fix `language-configuration.json` (lineComment is wrong today) |
| High | 2 — Fix `.conf` file association conflict |
| High | 1 — Convert grammar to JSON |
| Medium | 5 — Add missing interface types |
| Medium | 6 — Add missing top-level sections |
| Medium | 3 — Update engine target |
| Low | 7 — Add `.vscodeignore` |
| Low | 8 — Add CI / testing |
