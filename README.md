# @grml/lib-template

A template for an ESM TypeScript library: strict `tsc` to `dist/`, ESLint,
TypeDoc, commitlint, git-cliff and lefthook.

Bun comes first: it is the package manager (`bun.lock`; there is no
`package-lock.json`) and the main runtime. Node is a supported runtime too, so
the same tests run under both.

## Runtime support

- **Bun** (version pinned by `packageManager` in `package.json`): CI runs lint,
  typecheck, tests, build and docs on Linux, macOS and Windows.
- **Node 22 and 24** (`.nvmrc` pins 24): CI runs the tests on Linux, plus a
  pack check.

Library code may only use what both runtimes have: `node:*` modules and web
standards. `tsconfig.json` loads only Node's types, so a `Bun.*` global fails
the typecheck. If you need a Bun-only fast path, put it in its own module and
point a `"bun"` condition in `exports` at it, with the plain module as the
fallback.

## Tests

Tests are written once against `node:test` and `node:assert`, and both
runtimes run that file unchanged:

| Script | Runs |
| --- | --- |
| `bun run test` | both of the below, as the pre-push check should |
| `bun run test:bun` | `bun test` |
| `bun run test:node` | `node --test` through [tsx](https://tsx.is) |
| `bun run check:pack` | builds, packs the tarball, installs it into a scratch project and imports it under Bun and Node |

`check:pack` catches broken `exports`, `types` or `files`, which the in-repo
tests cannot see. It skips Node when Node is not installed, except in CI.

## Starting from it

1. Copy the directory, `git init`, then `bun install`. The `prepare` script
   installs the git hooks and needs a git repository, so `git init` comes
   first.
2. Set `name`, `description`, `repository` and `keywords` in `package.json`.
   The name is still the placeholder `@grml/lib-template`.
3. Replace `src/greet.ts`, its re-export in `index.ts`, and
   `tests/greet.test.ts`. Tests go under `tests/` as `*.test.ts`.

The tasks are the scripts in `package.json`. Only `dist/` is published, and
there is no publish workflow: build and `bun publish` with your own registry
settings.

Commits follow Conventional Commits and `./release.sh v1.2.0` cuts a release;
[black-garden-ants](https://github.com/Smiduweorc/black-garden-ants) explains
both. This copy of `release.sh` is older than the one there and takes only an
explicit version.
