# GitHub Actions - Update JS

Update JS dependencies in core Silverstripe modules, create new bundles and create pull-requests

## Usage:

It is intended to be [run on a schedule](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows#scheduled-events). You can copy the below example verbatim, and just modify the schedule to suit your use case.

This workflow can also be triggered manually. When triggering it manually there will be a dropdown that lets you specify the `branch_type` which is passed as an input.

**.github/workflows/update-js.yml**
```yml
name: Update JS

on:
  # Run on a schedule of twice per year
  schedule:
    - cron: '0 0 1 2,8 *'
  workflow_dispatch:
    inputs:
      branch_type:
        description: 'The branch type to run action on'
        required: true
        default: 'schedule'
        type: choice
        options:
          - 'schedule'
          - 'prev-major-curr-minor'
          - 'curr-major-curr-minor'
          - 'curr-major-next-minor'

permissions: {}

jobs:
  update-js:
    # Only run the cron on the account hosting this repository, not on the accounts of forks
    # Change '<account_name>' to match the name of the account hosting this repository
    if: (github.event_name == 'schedule' && github.repository_owner == '<account_name>') || (github.event_name != 'schedule')
    name: Update JS
    runs-on: ubuntu-latest
    permissions:
      contents: write
      pull-request: write
      actions: write
    steps:
      - name: Update JS
        uses: silverstripe/gha-update-js@v1
        with:
          branch_type: ${{ github.event_name == 'schedule' && 'schedule' || github.event.inputs.branch_type }}
```

### Inputs:

#### branch_type

The type of branch to run the action on. Examples assume that the current major is 6 and the latest stable release is 6.0

- `schedule` - Will dispatch the `update-js.yml` workflow on the repository that called this action three times, each time with one of the other `branch_type` input values e.g. `prev-major-curr-minor`.
- `prev-major-curr-minor` - The current minor on the latest major e.g. 5.4 branch. If this branch is no longer in support, which is determined by the existance of a 6.2.0 tag, then the job will complete with a green status without creating a pull-request.
- `curr-major-curr-minor` - Will target 6.0 branch
- `curr-major-next-minor` - Will target 6 branch. If a 6.4.0 tag exists, then the job will complete with a green status without creating a pull-request.
