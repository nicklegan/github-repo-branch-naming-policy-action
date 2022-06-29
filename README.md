# :no_good: GitHub Repository Branch Naming Policy Action

> A GitHub Action which prevents pull requests from being merged and sends issue notifications if a branch is not following the configured naming convention.

- When a [branch protection](https://docs.github.com/repositories/configuring-branches-and-merges-in-your-repository/defining-the-mergeability-of-pull-requests/about-protected-branches) rule is configured and [`Require status checks before merging`](https://docs.github.com/repositories/configuring-branches-and-merges-in-your-repository/defining-the-mergeability-of-pull-requests/about-protected-branches#require-status-checks-before-merging) is enabled, the head branch name of the pull request must follow the configured naming convention otherwise the pull request will be blocked from merging.

- When a branch is created or renamed, the name of the branch will be verified against the configured naming convention. If the name of the branch does not follow the configured naming convention, an issue with instructions including an @mention mentioning the creator will be opened.

- Once the incorrect name of the branch is resolved or the branch is deleted, the opened notification issue will be closed or optionally being deleted.

## Usage

```yaml
name: Branch Naming Policy Action

on:
  create:
  delete:
  pull_request:
    branches:
      - main

jobs:
  branch-naming-policy:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout
        uses: actions/checkout@v3

      - name: Run Branch Naming Policy Action
        uses: nicklegan/github-repo-branch-naming-policy-action@v1.1.0
        if: github.ref_type == 'branch' || github.ref_type == 'pull_request'
        with:
          token: ${{ secrets.GITHUB_TOKEN }}
          regex: '^([a-z0-9]+)-([a-z0-9]+)$'
        # flags: i
        # token: ${{ secrets.REPO_TOKEN }}
        # delete: true
```

## GitHub secrets

| Name                 | Value                                                                       | Location                     | Required |
| :------------------- | :-------------------------------------------------------------------------- | :--------------------------- | :------- |
| `REPO_TOKEN`         | An optional `repo` scoped [Personal Access Token] if the delete flag is set | [workflow.yml]               | `false`  |
| `ACTIONS_STEP_DEBUG` | `true`                                                                      | [Enables diagnostic logging] | `false`  |

[workflow.yml]: #Usage 'Usage'
[personal access token]: https://github.com/settings/tokens/new?scopes=repo&description=GitHub+Repository+Branch+Naming+Policy+Action 'Personal Access Token'
[enables diagnostic logging]: https://docs.github.com/actions/managing-workflow-runs/enabling-debug-logging#enabling-runner-diagnostic-logging 'Enabling runner diagnostic logging'

## Action inputs

| Name     | Description                                                                                                               | Default                     | Required |
| :------- | :------------------------------------------------------------------------------------------------------------------------ | :-------------------------- | :------- |
| `regex`  | A regex string matching correct repo naming conventions                                                                   | `^([a-z0-9]+)-([a-z0-9]+)$` | `true`   |
| `flags`  | Flag for repo naming regex string. e.g. `i` for case-insensitive                                                          | `i`                         | `false`  |
| `delete` | Deletes the issue instead of closing it when the branch name is resolved (requires a higher `repo` scoped workflow token) | `false`                     | `false`  |

## Regex examples

Regex example 1: `####-####` (2 fixed groups separated by a dash).

- `^([a-z0-9]+)-([a-z0-9]+)$`

Regex example 2: `prefix-####-####-*` (3 groups separated by dashes starting with a prefix).

- `^(prefix)-([a-z0-9]+)-([a-z0-9]+)`

Regex example 3: `####-####-####-*` (3 groups containing names and numbers divided by dashes).

- `^([a-z0-9]+)-([a-z0-9]+)-([a-z0-9]+)`

As a default the `i` flag is recommended to allow matches to be case-insensitive.

:bulb: For more info about regular expressions visit [Regular-Expressions.info](https://www.regular-expressions.info)
