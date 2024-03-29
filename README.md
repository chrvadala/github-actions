# chrvadala/github-actions

A collection of common Github Actions, useful to test and publish software. 

[![chrvadala](https://img.shields.io/badge/website-chrvadala-orange.svg)](https://chrvadala.github.io)
[![Donate](https://img.shields.io/badge/donate-GithubSponsor-green.svg)](https://github.com/sponsors/chrvadala)

# Actions
- [nodejs-test-library-action](https://github.com/chrvadala/github-actions#nodejs-test-library-action)
- [nodejs-release-library-action](https://github.com/chrvadala/github-actions#nodejs-release-library-action)
- [gh-pages-publish-action](https://github.com/chrvadala/github-actions#gh-pages-publish-action)

## nodejs-test-library-action

This composite action allows us to install, build and test a library.
Main steps:
- `npm install`
- `npm run build` (if-needed)
- `npm test`

### Inputs
| Input        | Description                  |
|--------------|------------------------------|
| NODE_VERSION | Node.js version to setup     |

### Outputs
No available outputs

### Example
```yaml
name: Test

on:
  push:
  pull_request:
  workflow_dispatch:

jobs:
  build:
    strategy:
      matrix:
        os: [ ubuntu-22.04 ]
        node: [ 14, 16, 18 ]
    name: Test Nodejs v${{ matrix.node }} on ${{ matrix.os }}
    runs-on: ${{ matrix.os }}
    steps:
      - uses: actions/checkout@v2
      - uses: chrvadala/github-actions/nodejs-test-library-action@v1
        with:
          NODE_VERSION: ${{ matrix.node }}
```

## nodejs-release-library-action

This composite action allows us to release a NodeJS library to npm.
Main steps:
- `npm ci`
- `npm run build` (if-needed)
- `npm version major/minor/patch`
- `npm test`
- create gh release
- `npm publish`


### Inputs
| Input        | Description                                                            |
|--------------|------------------------------------------------------------------------|
| NEXT_VERSION | The version that we want to release (one of `major`, `minor`, `patch`) |
| NPM_TOKEN    | Valid token to NPM                                                     |
| GITHUB_TOKEN | Valid token to Github                                                  |

### Outputs
| Output  | Description      |
|---------|------------------|
| VERSION | Released version |

### Example
```yaml
name: Release

on:
  workflow_dispatch:
   inputs:
      NEXT_VERSION:
        description: Define next version (major/minor/patch)
        required: true
      REPOSITORY_NAME:
        description: Repository full name (e.g. chrvadala/hello )
        required: true

jobs:
  build_and_release:
    runs-on: ubuntu-22.04
    if: ${{ github.event.inputs.REPOSITORY_NAME ==  github.repository }}
    steps:
      - uses: actions/checkout@v2
      - name: Release library
        uses: chrvadala/github-actions/nodejs-release-library-action@v1
        with:
          NEXT_VERSION: ${{ github.event.inputs.NEXT_VERSION }}
          NPM_TOKEN: ${{ secrets.npm_token }}
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```
## gh-pages-publish-action

This composite action allows us to publish and update a website hosted on gh-pages.
Main steps:
- `npm install`
- `npm run build` (if-needed)
- `npm run website:build`
- publish gh-pages

### Inputs
| Input        | Description                  |
|--------------|------------------------------|
| GITHUB_TOKEN | Valid token to Github   

### Outputs
No available outputs

### Example
```yaml
name: Website

on:
  push:
      branches:
        - main

jobs:
  build_and_publish:
    name: Publish Github Pages Website
    runs-on: ubuntu-22.04
    steps:
      - uses: actions/checkout@v2
      - uses: chrvadala/github-actions/gh-pages-publish-action@v1
        with:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```


## Contributors
- [chrvadala](https://github.com/chrvadala) (author)