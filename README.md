# Remote Branch Action

[![Test Action](https://github.com/JosiahSiegel/remote-branch-action/actions/workflows/test-action.yml/badge.svg)](https://github.com/JosiahSiegel/remote-branch-action/actions/workflows/test-action.yml)

## Synopsis

1. Create a branch on a remote repository.
2. [actions/checkout](https://github.com/actions/checkout) determins the active repo.

## Usage

```yml
jobs:
  test-action:
    name: Test create branch on ${{ matrix.os }}
    runs-on: ${{ matrix.os }}
    strategy:
      matrix:
        os: [ubuntu-latest]
    steps:
      - uses: actions/checkout@v4.1.1

      - name: Checkout test repo
        uses: actions/checkout@v4
        with:
          sparse-checkout: .

      - name: Checkout second repo
        uses: actions/checkout@v4
        with:
          sparse-checkout: .
          repository: me/second-repo
          token: ${{ secrets.SECONDARY_REPO_TOKEN }}
          path: second-repo

      - name: Test action
        id: test-action
        uses: ./
        with:
          branch: test-action

      - name: Test action on second repo
        id: test-action-second-repo
        uses: ./
        with:
          branch: test-action-second-repo
          path: second-repo

      - name: Get create branch status
        if: steps.test-action.outputs.create-status != 'false'
        run: echo ${{ steps.test-action.outputs.create-status }}
  
      - name: Get create branch status on second repo
        if: steps.test-action-second-repo.outputs.create-status != 'false'
        run: echo ${{ steps.test-action-second-repo.outputs.create-status }}
```

## Inputs

```yml
inputs:
  branch:
    description: Branch name
    required: true
  path:
    description: Relative path under $GITHUB_WORKSPACE to place the repository
    required: false
    default: '.'
```

## Outputs
```yml
outputs:
  create-status:
    description: Branch creation status
    value: ${{ steps.create-branch.outputs.create_status }}
```