# chrvadala/github-actions

A collection of common Github Actions, useful to test and release libraries. 

[![chrvadala](https://img.shields.io/badge/website-chrvadala-orange.svg)](https://chrvadala.github.io)
[![Donate](https://img.shields.io/badge/donate-PayPal-green.svg)](https://www.paypal.me/chrvadala/25)

# Actions
- [nodejs-test-library-action](https://github.com/chrvadala/github-actions#nodejs-test-library-action)
- [nodejs-release-library-action](https://github.com/chrvadala/github-actions#nodejs-release-library-action)

## nodejs-test-library-action

This composite action allows us to install, build and test a library.
Main steps:
- `npm install`
- `npm run build` (optional)
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
  schedule:
    - cron:  '13 0 1 * *'

jobs:
  build:
    strategy:
      matrix:
        os: [ ubuntu-20.04 ]
        node: [ 12, 14, 16 ]
    name: Test Nodejs v${{ matrix.node }} on ${{ matrix.os }}
    runs-on: ${{ matrix.os }}
    steps:
      - uses: actions/checkout@v2
      - uses: chrvadala/github-actions/nodejs-test-library-action@main
        with:
          NODE_VERSION: ${{ matrix.node }}
```

## nodejs-release-library-action

This composite action allows us to release a NodeJS library to npm.
Main steps:
- `npm ci`
- `npm run build` (optional)
- `npm version major/minor/patch`
- `npm test`
- `npm publish`
- `create gh release`


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
    runs-on: ubuntu-20.04
    if: ${{ github.event.inputs.REPOSITORY_NAME ==  github.repository }}
    steps:
      - uses: actions/checkout@v2
      - name: Release library
        uses: chrvadala/github-actions/nodejs-release-library-action@main
        with:
          NEXT_VERSION: ${{ github.event.inputs.NEXT_VERSION }}
          NPM_TOKEN: ${{ secrets.npm_token }}
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```


## Contributors
- [chrvadala](https://github.com/chrvadala) (author)