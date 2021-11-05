# chrvadala/github-actions

A collection of common Github Actions, useful to test and release libraries. 

[![chrvadala](https://img.shields.io/badge/website-chrvadala-orange.svg)](https://chrvadala.github.io)
[![Donate](https://img.shields.io/badge/donate-PayPal-green.svg)](https://www.paypal.me/chrvadala/25)

# Table of Contents
- [nodejs-test-library-action](https://github.com/chrvadala/github-actions#nodejs-test-library-action)

# Actions
## nodejs-test-library-action

This NodeJS action allow us to install, build and test a library

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



## Contributors
- [chrvadala](https://github.com/chrvadala) (author)