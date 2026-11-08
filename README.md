# @grml/lib-template

A template for an ESM TypeScript library: strict `tsc` to `dist/`, Node's test
runner via [tsx](https://tsx.is), ESLint, TypeDoc, commitlint, git-cliff and
lefthook. CI runs Node 22 and 24 on Linux, macOS and Windows; `.nvmrc` pins 24.

## Starting from it

1. Copy the directory, `git init`, then `npm install`. The `prepare` script
   installs the git hooks and needs a git repository, so `git init` comes
   first.
2. Set `name`, `description`, `repository` and `keywords` in `package.json`.
   The name is still the placeholder `@grml/lib-template`.
3. Replace `src/greet.ts`, its re-export in `index.ts`, and
   `tests/greet.test.ts`. Tests go under `tests/` as `*.test.ts`.

The tasks are the scripts in `package.json`. Only `dist/` is published, and
there is no publish workflow: build and `npm publish` with your own registry
settings.

Commits follow Conventional Commits and `./release.sh v1.2.0` cuts a release;
[black-garden-ants](https://github.com/Smiduweorc/black-garden-ants) explains
both. This copy of `release.sh` is older than the one there and takes only an
explicit version.
