# chrvadala/github-actions

Reusable GitHub Actions and workflow templates for Node.js projects, release automation, and GitHub Pages publishing.

[![chrvadala](https://img.shields.io/badge/website-chrvadala-orange.svg)](https://chrvadala.github.io)
[![Donate](https://img.shields.io/badge/donate-GithubSponsor-green.svg)](https://github.com/sponsors/chrvadala)

## Available assets

This repository currently includes:

- reusable workflow for Node.js library testing in [.github/workflows/test-nodejs.yml](.github/workflows/test-nodejs.yml)
- reusable workflow for Node.js library release in [.github/workflows/release-nodejs.yml](.github/workflows/release-nodejs.yml)
- composite action for publishing a website to GitHub Pages in [gh-pages-publish-action/action.yml](gh-pages-publish-action/action.yml)

## test-nodejs workflow

This reusable workflow runs a matrix-based test job for a Node.js library.

### Inputs
| Input | Description |
| --- | --- |
| node-versions | JSON array of Node.js versions to test, for example `["18", "20", "22"]` |
| command | Optional multiline shell command executed before dependency installation |
| coveralls_enabled | Boolean flag to enable or disable upload to Coveralls. Defaults to `true`. |

### What it does
- checks out the repository
- sets up Node.js
- optionally runs a custom command
- runs `npm ci`
- runs `npm run build --if-present`
- runs `npm test`
- uploads coverage to Coveralls

### Example
```yaml
name: Test

on:
  push:
  pull_request:
  workflow_dispatch:

jobs:
  test:
    uses: chrvadala/github-actions/.github/workflows/test-nodejs.yml@v2
    permissions:
      contents: read
      checks: write
      pull-requests: write
    with:
      node-versions: '[24,26]'
      coveralls_enabled: false
      command: |
        echo "Custom setup"
```

## release-nodejs workflow

This reusable workflow validates the requested release level, prepares the package, runs tests, and publishes to npm.

### Inputs
| Input | Description |
| --- | --- |
| next-version | Release bump to apply: `major`, `minor`, or `patch` |
| command | Optional multiline shell command executed before dependency installation |

### Outputs
| Output | Description |
| --- | --- |
| version | The released package version |

### What it does
- validates the requested release type
- optionally runs a custom pre-install command
- installs dependencies and runs a build if present
- bumps the package version with `npm version`
- packs the library
- runs the project test suite
- performs a dry-run publish
- pushes the version tag and commit
- publishes the package to npm

### Example
```yaml
name: Release

on:
  workflow_dispatch:
    inputs:
      next-version:
        description: Define next version (major/minor/patch)
        required: true
      repository-name:
        description: Repository full name
        required: true

jobs:
  release:
    if: ${{ github.event.inputs['repository-name'] == github.repository }}
    uses: chrvadala/github-actions/.github/workflows/release-nodejs.yml@v2
    permissions:
      contents: write
      packages: write
      id-token: write
    with:
      next-version: ${{ github.event.inputs.next-version }}
      command: |
        echo "Prerelease setup"
```

## gh-pages-publish-action

This composite action publishes the generated website to GitHub Pages.

### Inputs
| Input | Description |
| --- | --- |
| GITHUB_TOKEN | GitHub token used to update the gh-pages branch |

### What it does
- sets up Node.js
- installs dependencies
- builds the project
- runs `npm run website:build`
- uploads the generated website artifact
- publishes the `build-website` folder to `gh-pages`

### Example
```yaml
name: Publish website

on:
  push:
    branches:
      - main

jobs:
  publish:
    runs-on: ubuntu-26.04
    steps:
      - uses: actions/checkout@v7
      - uses: chrvadala/github-actions/gh-pages-publish-action@v2
        with:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

## Contributors
- [chrvadala](https://github.com/chrvadala) (author)