# Publishing

`.github/workflows/publish.yml` stages a release on npm from GitHub Actions.
npm signs in through trusted publishing (OIDC), so there is no `NPM_TOKEN`
secret, and the version gets a provenance statement. A staged version stays
private until you approve it with 2FA.

The workflow ships commented out. Uncomment it once `package.json` has the
real name, then do the setup below once per package.

## Setup

Uncomment `publish.yml` and push it to the default branch. GitHub only lets
you start a workflow that is on the default branch.

Create the `npm` environment the workflow runs in:

```
gh api -X PUT repos/<owner>/<repo>/environments/npm
```

The package has to exist on npm before it can have a trusted publisher. For
a new package, publish the first version by hand with `npm publish`
(scoped packages also need `--access public`), then add the trusted
publisher. It needs a 2FA code:

```
npm trust github <package> --file publish.yml --repo <owner>/<repo> --env npm --allow-stage-publish -y --otp=<code>
npm trust list <package> --otp=<code>
```

`--allow-stage-publish` without `--allow-publish` means the workflow can only
stage. On npmjs.com, set the package's publishing access to "Require
two-factor authentication and disallow bypass 2fa tokens" as well.

## Releasing

```
./release.sh v1.2.0
git push && git push --tags
gh workflow run publish.yml -f tag=v1.2.0
npm stage list <package>
npm stage approve <stage-id>
```

The workflow refuses a tag that does not match the `package.json` version or
is not on the default branch. Prerelease tags (`v1.2.0-rc.1`) are staged under
the `next` dist-tag.

## Known quirks

- The staging step fails with `E401 Unable to authenticate` or `ENEEDAUTH
  need auth` when npm has no trusted publisher matching the run. Check
  `npm trust list <package>`: the repo, workflow file name and environment
  must match exactly, including case. npm keeps one trusted publisher per
  package; to change it, `npm trust revoke <package> --id=<id>` first.
- Do not set `registry-url` on `actions/setup-node`. It writes a placeholder
  token into `.npmrc`, npm sends that instead of the OIDC token, and staging
  fails with `E401`.
- Scoped packages (`@scope/name`) stage as restricted by default. Add
  `--access public` to both `npm stage publish` lines in the workflow.
