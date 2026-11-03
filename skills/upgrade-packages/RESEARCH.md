# Researching a family

You're writing the **brief** for one family of packages: what changed across its whole range (every version after current, up to and including target) and what that means for this codebase. The upgrade is done from your brief alone, so a change missing from it ships as a bug.

## Read every version

Notes for a version often live in only one place. Work down this list until every version in the range is covered:

1. **GitHub Releases** of the source repo (`gh release list -R <owner/repo>`, `gh release view <tag> -R <owner/repo>`). The registry metadata names the repo (`npm view <pkg> repository.url`).
2. **CHANGELOG.md** at the target's tag. Monorepos keep one per package (`packages/<name>/CHANGELOG.md`) and tag releases as `<pkg>@<version>`.
3. **Upgrade and migration guides** for every major in the range. Frameworks publish these on their docs site, separate from the changelog, often with a codemod.
4. **Release blog posts** for majors and large minors. They explain features the changelog only names.
5. **Current docs** for the APIs we use: Context7 when available, otherwise the docs site.

From the registry, also read target's `engines` and `peerDependencies`, and any deprecation notice (`npm view <pkg> deprecated`). A deprecated or renamed package means migrating to its successor; research the successor as part of this brief.

If target was published within the last week, scan the issue tracker for regressions reported against it.

## Map it onto our code

For every breaking change, deprecation, silent change, and feature, search the repo for where it lands (imports, call sites, config keys, CLI flags, types) and cite `file:line`. A breaking change with no hits is recorded as "no usage", with the search you ran.

**Silent changes** (a new default, changed output, different timing) get extra care: no check fails on them, so the brief is the only place they surface.

Hunt for **workarounds**: code that exists because an older version fell short. Comments linking upstream issues, `patches/` or `patchedDependencies`, `overrides`/`resolutions`, shims, polyfills, `@ts-expect-error` or casts around the package's types. For each, check whether a release in the range fixes it.

Classify each feature:

- **Adopt** when it subtracts from our code or our risk: it replaces a deprecated API we call, deletes a workaround or hand-rolled code the package now covers, is the new documented default for something we do, or fixes a bug or performance problem we have.
- **Optional** for everything else worth knowing.

## Brief

Return this, terse, nothing else:

```
## <family>: <current> → <target>
Sources: <every URL read>
Versions: <each version in the range with a one-line summary; a run of patch versions may share a line; "no notes (checked: releases, changelog)" where none exist>
Requirements: <engine and peer changes, and what in our repo they force>
Breaking (affects us): <change> — <file:line hits> — <migration step or codemod>
Breaking (no usage): <change> — <search run>
Deprecations: <api> — <file:line hits> — <replacement>
Silent changes: <change> — <file:line hits>
Workarounds removable: <file:line> — <version that fixed it>
Adopt: <feature> — <file:line> — <why it qualifies>
Optional: <feature> — <where it would apply>
Risks: <regressions reported against target, anything uncertain>
Blockers: <why target can't be reached, or "none">
```

Done when every version in the range appears under Versions and every breaking change and deprecation carries hits or a recorded search.
