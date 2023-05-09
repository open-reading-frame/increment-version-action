# increment-version-action

This action will create a new tag based on a version in a JSON file in source control, and on pushes to specified branches will push the newly created tag.
This is intended to help automate semantic versioning (https://semver.org/) tags for repositories for each PR merge to main (or another default branch).
Version tags will be generated in the format `major.minor.patch-release` with a date string optionally appended to the tag if specified in the calling workflow.
If the version is not changed for a given PR, the `release` number will be incremented by 1, intended to reflect changes to things like process, build, docs, tests, etc. that do not impact actual code.
The new version will be set as an output of the action called `outputs.new-version`.
This value can be used in subsequent steps/jobs where a version identifier is needed.

## A note on tag protection

## Example

```yaml
# This template is intended to be incorporated as a workflow in another repository to semi-automatically increment
# an internal/external version number for a repository
name: tag-pr-merge

on:
  push:
    branches: [main]  # Or the name of your default branch
  pull_request:
    types: [opened, synchronize, reopened]

jobs:
  increment-version:
    runs-on: ubuntu-latest
    outputs:
      new-version: ${{ steps.run-increment-version.outputs.new-version }}
    steps:
      - name: Run the increment-version action
        id: run-increment-version
        uses: open-reading-frame/increment-version-action@main
        with:
          json: <path/to/repo-data.json>
          use-date: <true|false>, append date string to generated version tag
          protect-tag: <true|false>, protect the generated tag in GitHub
          token: ${{ secrets.MY_TOKEN }}, if not provided will default to github.token
```
