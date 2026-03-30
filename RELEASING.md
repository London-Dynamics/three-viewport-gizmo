# Releasing from the London Dynamics Fork

This repo supports two parallel workflows:

- Contribute changes upstream through normal pull requests.
- Ship fast releases from this fork when needed.

## Branch model

- `main`: Keep aligned with upstream `main` as closely as possible.
- `release/ld`: Release branch for fork-only or not-yet-upstreamed changes.
- `feature/*`: Work branches for new changes.

## One-time setup

Make sure your remotes are configured:

```bash
git remote -v
git remote add upstream https://github.com/Fennec-hub/three-viewport-gizmo.git
git fetch upstream
```

Create the release branch once:

```bash
git checkout main
git pull --ff-only origin main
git checkout -b release/ld
git push -u origin release/ld
```

## Daily development flow

1. Branch from `main` for new work (`feature/...`).
2. Open PRs upstream for upstreamable changes.
3. Merge into `release/ld` when you need to ship immediately.

## Fast release flow (GitHub Actions)

Use the `Release fork package` workflow from the Actions tab while on `release/ld`.

Inputs:

- `release_type`:
  - `prerelease` (recommended for fork releases)
  - `patch`, `minor`, `major` (for stable release numbering)
- `preid` (for prerelease): defaults to `ld`

What the workflow does:

1. Installs dependencies.
2. Builds the package.
3. Bumps version in `package.json` and `package-lock.json`.
4. Creates a git tag.
5. Packs the tarball (`npm pack`).
6. Publishes `@london-dynamics/three-viewport-gizmo` to GitHub Packages.
7. Pushes commit + tag.
8. Publishes a GitHub Release with the tarball attached.

## Installing the scoped package

Add GitHub Packages auth in your project/user `.npmrc`:

```ini
@london-dynamics:registry=https://npm.pkg.github.com
//npm.pkg.github.com/:_authToken=${GITHUB_TOKEN}
```

Then install:

```bash
npm install @london-dynamics/three-viewport-gizmo
```

## Versioning recommendation

Prefer prereleases for fork velocity:

- `2.2.1-ld.0`
- `2.2.1-ld.1`
- `2.2.1-ld.2`

When upstream publishes a new stable line, sync first, then continue prereleases from that base.

## Sync fork with upstream updates

```bash
git checkout main
git fetch upstream
git merge --ff-only upstream/main
git push origin main

git checkout release/ld
git merge main
git push origin release/ld
```

If conflicts happen in `release/ld`, resolve them there once, then continue releasing.
