# GitHub Actions - Update JS

Update JS dependencies in core Silverstripe modules, create new bundles and create pull-requests

## Usage:

This action has no inputs. It is intended to be [run on a schedule](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows#scheduled-events). You can copy the below example verbatim, and just modify the schedule to suit your use case.

**.github/workflows/update-js.yml**
```yml
name: Update JS

on:
  # Run on a schedule of once per quarter
  schedule:
    - cron: '0 0 1 */3 *'
  workflow_dispatch:

jobs:
  update-js:
    # Only run the cron on the account hosting this repository, not on the accounts of forks
    # github.repository take the form of AccountName/repository-name
    # Change '<account_name>/' to match the name of the account hosting this repository
    if: startsWith(github.repository, '<account_name>/')
    name: Update JS
    runs-on: ubuntu-latest
    steps:
      - name: Update JS
        uses: silverstripe/gha-update-js@v1
```
