# Configure setup-go to use stable, oldstable and go.mod specified Go versions

This repository demonstrates we can use [setup-go](https://github.com/actions/setup-go) GHA action with [`go-version`](https://github.com/actions/setup-go#matrix-testing) and [`go-version-file`](https://github.com/actions/setup-go#go-version-file) settings at same time.

The workflow file `.github/workflows/go.yaml` configures a matrix build that tests multiple Go versions, including stable, oldstable, and configurations that fall back on the `go.mod` file for version specification.

This setup addresses the issue discussed in https://github.com/actions/setup-go/issues/450 and https://github.com/actions/setup-go/issues/547

Here is the relevant section of the workflow file:

```yaml
# SPDX-License-Identifier: MIT
# SPDX-FileCopyrightText: Copyright (c) 2025 Christophe Colombier (https://github.com/ccoveille)
jobs:
  show-go-version:
    name: Show Go Version
    permissions:
      contents: read  # for actions/checkout to fetch code
    runs-on: ubuntu-latest
    strategy:
      fail-fast: false
      matrix:
        go-version:
          - 'stable'
          - 'oldstable'
          - ''  # do not specify go-version to fall back on go-version-file configuration
    steps:
      - name: checkout-action
        uses: actions/checkout@v5

      - name: Set up Go
        uses: actions/setup-go@v6
        with:
          go-version: ${{ matrix.go-version }}
          go-version-file: "go.mod" # this one is only used when go-version is not specified.

      - name: Display Go version
        run: go version

      - name: Run tests with the selected Go version
        run: go test ./...
```

