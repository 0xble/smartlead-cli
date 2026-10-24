# Maintenance

## Background

This source-upkeep contract covers the owned fork
[0xble/smartlead-cli](https://github.com/0xble/smartlead-cli), maintained branch
`master`, with canonical checkout `/Users/brianle/Repos/smartlead-cli`.
Upstream is [Smartlead-Public/smartlead-cli](https://github.com/Smartlead-Public/smartlead-cli),
default branch `master`. The accepted upstream source baseline is
[ea9a6e450af1216769d88dc354517503f9848a90](https://github.com/Smartlead-Public/smartlead-cli/commit/ea9a6e450af1216769d88dc354517503f9848a90).
On 2026-10-24, freshly fetched owned and upstream default refs both resolved to
that revision. External source drift and the separation of owned-fork delivery
from upstream release identity are the maintenance problem.

## Preserve

- `fork` identifies the owned repository. `origin` identifies upstream, not the
  publication destination. Publish source branches and PRs only to
  `0xble/smartlead-cli`, never push to `origin`.
- Preserve upstream application behavior. This contract introduces only source
  governance, not an accepted application patch stack. A contribution branch or
  an upstream PR is not maintained-default source merely because the fork owns
  its head. Keep prospective contributions separate until an explicit adoption
  decision, without rewriting or borrowing another contribution owner's work.
- [README.md](README.md) owns CLI usage and installation. The package identity
  in [package.json](package.json), `@smartlead/cli`, and the README's upstream
  release links do not identify a fork build or prove it is installed. Source
  synchronization, fork publication, package release, and runtime activation
  require separate evidence.

## Update

1. Fetch `fork/master` and `origin/master`, verify their repository identities,
   and compare the accepted upstream revision with current upstream source and
   the maintained fork default. Develop from the fetched owned default in an
   owned linked worktree, leaving the canonical checkout unchanged.
2. Adopt upstream changes through a scoped PR targeting `master` on
   `0xble/smartlead-cli`. Preserve this contract as fork-only governance. Do not
   include an unmerged contribution branch as part of a default-branch sync.
   If an application divergence is explicitly accepted later, record its
   rationale, provenance, regression proof, upstream disposition, fork delivery,
   and retirement condition with that adoption, rather than inventing a patch
   record for a prospective contribution.
3. Review [publish.yml](.github/workflows/publish.yml) and
   [release.yml](.github/workflows/release.yml) before landing: the former can
   publish npm on `master` pushes, and the latter publishes on version tags.
   Source-upkeep authority does not authorize those release effects. If landing
   cannot remain within authorized source-only effects, leave the PR blocked
   with the specific release prerequisite. Do not create release tags or change
   publishing credentials as an upkeep shortcut.
4. Advance the accepted baseline in `Background` only when the referenced
   upstream revision has actually been adopted into the maintained default.
   Immediately before reporting `Updated` or `Already current`, fetch upstream
   again and prove zero upstream-only commits against the delivered default.
   A pending PR, failed proof, or lagging default is `Blocked`, with exact refs,
   the failed stage, and remaining action, not a successful synchronization.

## Verify

- For a contract-only candidate, prove that the complete base-to-head file set
  is only `MAINTENANCE.md`, check whitespace and Markdown links, and verify root
  discovery binds the exact candidate HEAD and contract bytes. Use the existing
  discovery dispatcher without a per-fork job, frequency, or support directory.
- For source adoption that changes application files, use the local TypeScript
  check from [ci.yml](.github/workflows/ci.yml) and the build script from
  [package.json](package.json), plus focused regressions for adopted behavior.
  There is no test script at the accepted baseline. Do not present compilation
  as behavioral coverage or claim unexecuted checks passed. Do not invoke the
  provider CLI or read live configuration to prove source upkeep.
- After the final fetch, run
  `git rev-list --left-right --count origin/master...fork/master` and require
  the left count to be `0`. Record both resolved SHAs and prove ancestry with
  `git merge-base --is-ancestor origin/master fork/master`, explaining any
  fork-only commits. After authorized source
  publication, require the delivered local default SHA to equal the owned
  remote default SHA, with a direct remote-ref readback. A draft branch's SHA
  parity is publication proof only, not proof that the maintained default has
  adopted it or that a runtime is using it.
