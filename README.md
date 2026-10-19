<!-- The spec text here mirrors index.html. Change both together. -->

<p align="center">
  <img src="logo.svg" alt="LiteVer" width="350" />
</p>

# Lite Versioning (LiteVer)

> Two numbers. One question: **will this break me?**

`MAJOR.MINOR`

LiteVer is Semantic Versioning without the extra number. It keeps the one promise
people actually rely on, **MAJOR means breaking, anything else is safe**, and drops
the distinction between a feature and a fix that almost nobody acts on.

Lighter, not lesser. If you don't need the extra number, why carry it?

---

## Summary

A version number takes the form `MAJOR.MINOR`:

```
  2   .   7
  │       │
  │       └─ MINOR  bumped on every release that won't break you   (safe)
  └───────── MAJOR  bumped when a release will break you           (look first)
```

- **MAJOR**: bump it when a release is **not backward compatible**, so the people
  and software depending on you may have to change something to take it. Resets
  MINOR to 0. `0` is reserved for initial development; ship your first stable release
  as `1.0` and go from there.
- **MINOR**: bump it for **every other release**: new features, bug fixes,
  performance work, deprecations, anything that keeps the contract. Safe to take
  without looking.

Where a tool insists on three numbers, write `2.7.0`. The trailing `0` is padding:
always `0`, never bumped.

---

## Introduction

Semantic Versioning asks three questions of every release: _did it break the
contract, did it add something, or did it only fix something?_ The people who depend
on you act on the first answer and mostly ignore the other two.

Look at how SemVer versions are consumed. npm saves `^1.4.2` by default, which takes
every MINOR and PATCH release automatically. A Cargo requirement of `"1.4"` does the
same, and so does Ruby's `~> 1.4`. The difference between a feature and a fix is
worked out carefully by the publisher, then thrown away by almost every consumer.

It isn't free to work out, either. Every release starts with a small debate: is this
a feature or a fix? Does a bug fix that adds an option count as a feature? Commit
conventions, changelog tooling, and release bots exist largely to settle it.

LiteVer keeps the promise people rely on and drops the one they don't. A version is
`MAJOR.MINOR`: a MAJOR bump tells you to look before you upgrade; a MINOR bump tells
you that you don't have to.

LiteVer deliberately says nothing about the size of a change. A one-line fix and a
big new feature both bump MINOR by one, because neither breaks anything. A tiny
removal bumps MAJOR, because it does.

---

## LiteVer Specification

The key words "MUST", "MUST NOT", "SHOULD", "SHOULD NOT", and "MAY" are to be
interpreted as described in [RFC 2119](https://www.rfc-editor.org/rfc/rfc2119).

1. A project using LiteVer MUST declare what it promises to the people and software
   that depend on it: its public API, interface, or documented behaviour. This is
   **the contract**. It SHOULD be precise and comprehensive, whether it lives in code
   or in documentation.
1. A normal version number MUST take the form `X.Y`, where `X` (MAJOR) and `Y`
   (MINOR) are non-negative integers and MUST NOT contain leading zeroes. They are two
   separate integers, not a decimal: `1.10` comes after `1.9`. Each successive release
   MUST increase in precedence, as defined in rule 7.
1. Once a version has been released, its contents MUST NOT change. Any change MUST be
   released as a new version.
1. **MAJOR `0`** is for initial development. Anything MAY change at any time, and the
   contract SHOULD NOT be considered stable. Every `0.Y` release MAY be breaking.
1. **MAJOR (`X`)** MUST be incremented when a release makes any backward-incompatible
   change to the contract. It MAY also be incremented without one, but consumers will
   treat every MAJOR bump as breaking. When MAJOR is incremented, MINOR MUST be reset
   to `0`.
1. **MINOR (`Y`)** MUST be incremented for every release that keeps the contract
   backward compatible: new features, bug fixes, deprecations, performance work,
   internal changes, and anything else. LiteVer draws no distinction between a
   feature and a fix.
1. **Precedence** is determined by comparing MAJOR, then MINOR, numerically
   (`0.9 < 1.0 < 1.9 < 1.10 < 2.0`).
1. **Padding.** The canonical form has two components. Where a tool, registry, or
   ecosystem requires three, a version MUST be written as `X.Y.0`. The third component
   is padding: it MUST always be `0`, MUST NOT be incremented, and carries no meaning.
   `X.Y` and `X.Y.0` denote the same version. A project SHOULD use one written form
   per ecosystem, consistently.
1. Identifiers MAY be appended: a hyphen introduces pre-release identifiers (e.g.
   `2.0-rc.1`, padded `2.0.0-rc.1`) and a plus sign introduces build metadata (e.g.
   `2.0+20261019`). Their syntax and precedence follow SemVer 2.0.0: a pre-release
   version has lower precedence than its normal version, and build metadata MUST be
   ignored when determining precedence.
1. **One line per MAJOR.** Within a MAJOR version, releases form a single sequence.
   A fix for an older MAJOR line is released as the next MINOR on that line (with
   `2.x` current and `1.9` the latest `1.x`, a backported fix ships as `1.10`). Earlier
   MINOR versions within a line are not maintained separately, because every later
   MINOR in the same line is a compatible upgrade.
1. **Initial version and going stable.** A project SHOULD begin at `0.1`. Version
   `1.0` defines the first stable contract; a project SHOULD release it once its
   contract is stable, or as soon as others depend on it in production, whichever
   comes first.
1. **Scope.** LiteVer is for anything that offers a contract to others and whose
   consumers need one answer from a version number: _can I take this without changing
   anything?_ That includes libraries, packages, APIs, command-line tools, plugins,
   file formats, and specifications.

---

## Why use LiteVer

- **One question, one number.** `2.7 → 2.8` is safe to take. `2.8 → 3.0` means read
  the notes first. The version answers the only question a consumer acts on.
- **It ends the feature-or-fix argument.** No more debating whether a fix that adds
  an option "deserves" a MINOR or a PATCH. If it doesn't break anything, it's MINOR.
- **It speaks your tools' language.** Padded to `X.Y.0`, a LiteVer version is a
  valid SemVer string. Caret ranges, Cargo requirements, Ruby's `~>`, and sort orders
  all work unchanged. Your consumers don't need to know you use LiteVer.
- **It's how people already talk.** Nobody says "we're on two point seven point
  three". They say "we're on 2.7". LiteVer just makes that the whole version.
- **Easy in, easy out.** Moving from SemVer needs no renumbering, and moving back is
  just as easy (see the FAQ).

---

## When NOT to use LiteVer

LiteVer trades away SemVer's feature/fix distinction on purpose. It is the wrong tool
when:

- **Your consumers rely on patch-only updates.** If downstream users (long-term
  support distributions, regulated environments, slow-moving enterprise deployments)
  need to take fixes without taking features, they need SemVer's PATCH line
  (`~1.4.2`). LiteVer gives that up.
- **You maintain several MINOR lines at once.** If you support `2.3` and `2.4` side by
  side with separate fixes, you need a third number to tell them apart.
- **Your app ships through a native and an over-the-air channel.** For a React
  Native or Expo app, the useful question is how a release reaches users, not whether
  a contract broke. Use [PaceVer](https://github.com/pvinis/pacever).
- **Your audience keys off recency.** If readers of your version number mostly want
  "how recent", use [CalVer](https://calver.org).

Adopting LiteVer means giving up patch-only update ranges in exchange for a simpler
number and a simpler release process. Make that trade on purpose.

---

## Example: one library's version history

| Version | Change | What shipped                                                                 |
| ------- | ------ | ---------------------------------------------------------------------------- |
| `0.1`   | start  | First release. Initial development begins.                                   |
| `0.2`   | MINOR  | Renamed the main function. In `0.x`, anything may change.                    |
| `1.0`   | MAJOR  | The contract is declared stable.                                             |
| `1.1`   | MINOR  | Fixed a crash on empty input.                                                |
| `1.2`   | MINOR  | Added a `timeout` option.                                                    |
| `1.3`   | MINOR  | Deprecated `connect()` in favour of `open()`.                                |
| `2.0`   | MAJOR  | Removed `connect()`. MINOR resets.                                           |
| `1.4`   | MINOR  | A security fix, backported to the `1.x` line for users not yet on `2.0`.     |
| `2.1`   | MINOR  | The same security fix on the current line.                                   |

Every row answers _"can I take this without looking?"_ from the number alone: a MINOR
change means yes; a MAJOR change means read the notes first.

---

## Writing LiteVer in your ecosystem

| Ecosystem      | Write it as | Notes                                                                                     |
| -------------- | ----------- | ----------------------------------------------------------------------------------------- |
| npm            | `2.7.0`     | npm requires three parts, so pad. Dependents' `^2.7.0` takes every compatible release.     |
| Cargo (Rust)   | `2.7.0`     | Pad the `version` field. Dependents can already write `"2.7"`, meaning `>=2.7, <3.0`.     |
| Go modules     | `v2.7.0`    | Go requires three parts and a `v`. MAJOR 2 and up goes in the module path, as usual.      |
| PyPI (PEP 440) | `2.7`       | Two parts are native. Dependents' `~=2.7` means `>=2.7, ==2.*`.                           |
| RubyGems       | `2.7`       | Two parts are native. Dependents' `~> 2.7` means `>= 2.7, < 3.0`: exactly LiteVer's promise. |
| Git tags       | `v2.7`      | Or `v2.7.0` if your release tooling expects three parts.                                  |

---

## FAQ

**Where did PATCH go?**
Into MINOR. A bug fix is a backward-compatible change, so it bumps MINOR like any
other. LiteVer doesn't ask whether a release fixed something or added something, only
whether it breaks anything.

**Isn't `1.10` lower than `1.9`?**
No. Versions aren't decimals: each component is its own integer, so `1.10` comes
after `1.9` (rule 7). Watch out for config formats that _do_ read them as decimals.
Unquoted, `version: 1.10` in YAML or `version = 1.10` in TOML is the number `1.1`.
Always write the version as a string: `version: "1.10"`.

**My tool requires three numbers.**
Pad it: `2.7` becomes `2.7.0` (rule 8). The trailing `0` never changes.

**Is a padded LiteVer version valid SemVer?**
In form, yes: `X.Y.0` is a valid SemVer string and sorts correctly. In meaning, almost.
SemVer asks for a PATCH bump on fix-only releases, while LiteVer folds those into
MINOR. The part that tools and people rely on is identical (MAJOR means breaking,
anything else is safe), so nobody reading your version as SemVer gets a wrong answer
about compatibility. At worst they think a bug fix added a feature.

**How do I ship a hotfix for an older version?**
As the next MINOR on that MAJOR line (rule 10). With `2.x` current and `1.9` the last
`1.x` release, the fix goes out as `1.10`, and as the next `2.x` too. You never need
to fix `2.4` separately from `2.5`: `2.5` is a compatible upgrade from `2.4`, so the
fix ships as `2.6` and everyone on `2.x` can take it.

**What about users who want fixes but not features?**
LiteVer doesn't serve them, on purpose (see _When NOT to use LiteVer_). If that
audience matters to you, SemVer's PATCH number exists exactly for them.

**Can I bump MAJOR for a big release that doesn't break anything?**
You may (rule 5), but every MAJOR bump makes your consumers stop and check. Save MAJOR
for breaks, and celebrate big compatible releases in your release notes, not in the
number.

**What counts as a breaking change?**
Anything that, under the contract you declared (rule 1), could make a consumer change
something to take the release: removing or renaming public API, changing documented
behaviour, tightening what input you accept. When in doubt, it's MAJOR. A cautious
MAJOR costs a consumer a look; a wrong MINOR costs them a broken build.

**How should I handle deprecations?**
Deprecate in a MINOR release, remove in the next MAJOR. Marking something deprecated
breaks nothing; removing it does.

**In SemVer's `0.x` I use MINOR for breaks and PATCH for fixes. Can I do that here?**
No. That's three numbers doing two numbers' job. In LiteVer's `0.x` anything may break
(rule 4), and tools agree: npm and Cargo already treat each padded `0.Y.0` as its own
breaking line. If people depend on your project, it's time for `1.0`. MAJOR numbers are
free.

**I already use SemVer. How do I migrate?**
Drop PATCH from your next release. At `3.4.2`, your next compatible release is `3.5`
(or `3.5.0` padded) and your next breaking release is `4.0`. There's no renumbering, and
precedence holds because `3.5` comes after `3.4.2`. Going back is just as easy: `3.5`
is `3.5.0`, and you start bumping PATCH again.

**Is LiteVer versioned with LiteVer?**
Yes. A spec is a contract that other people rely on, which is exactly what LiteVer is
for. Its sibling PaceVer can't say the same: PaceVer is for apps, so it versions itself
with SemVer.

---

## About

LiteVer was created in 2026 by [Pavlos Vinieratos](https://github.com/pvinis). It is a
sibling of [PaceVer](https://github.com/pvinis/pacever), which versions apps by the
pace each release travels at.

This specification is versioned with **LiteVer** itself.

Current version: **1.0** (LiteVer).

## License

LiteVer, the Lite Versioning specification, is licensed under a
[Creative Commons Attribution 4.0 International License (CC BY 4.0)][cc-by]. You are free to
share and adapt it, including commercially, as long as you give appropriate credit.

Copyright © 2026 [Pavlos Vinieratos](https://github.com/pvinis).

[cc-by]: https://creativecommons.org/licenses/by/4.0/
