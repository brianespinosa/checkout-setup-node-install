# checkout-setup-node-install

![Last Updated](https://img.shields.io/github/last-commit/brianespinosa/checkout-setup-node-install?label=Last%20Updated&cacheSeconds=120)
![Project License](https://img.shields.io/github/license/brianespinosa/checkout-setup-node-install?label=Project%20License&color=blue)

Composite action that will check out our code, set up node and yarn with caching, and install yarn dependencies

```
brianespinosa/checkout-setup-node-install@main
```

## Usage

```yaml ci.yml
name: CI

on:
  push:
    branches: [main]
  pull_request:

jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: brianespinosa/checkout-setup-node-install@main
      - run: yarn lint

  test:
    runs-on: ubuntu-latest
    steps:
      - uses: brianespinosa/checkout-setup-node-install@main
      - run: yarn test

  build:
    runs-on: ubuntu-latest
    needs: [lint, test]
    steps:
      - uses: brianespinosa/checkout-setup-node-install@main
      - run: yarn build
```

## Assumptions

- We are using a modern version (v2+) of `yarn` as a package manager
- We are using a `.nvmrc` file to define the version of `node` to use
- We do not need to checkout the entire git history (tags, etc)
- We do not want to change the `yarn.lock` file at all during install