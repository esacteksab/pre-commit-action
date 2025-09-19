# pre-commit/action

This is a fork of the GitHub [pre-commit/action](https://github.com/pre-commit/action) to run [pre-commit](https://pre-commit.com), which is in maintenance mode only. In pins the `action/cache` in `action.yml` so that you can [require that all actions are pinned to full-length commit SHA's](https://github.blog/changelog/2025-08-15-github-actions-policy-now-supports-blocking-and-sha-pinning-actions/).

## using this action

To use this action, make a file `.github/workflows/pre-commit.yml`.  Here's a
template to get started:

```yaml
name: pre-commit

on:
  pull_request:
  push:
    branches: [main]

permissions:
    contents: read

jobs:
  pre-commit:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@08c6903cd8c0fde910a37f88322edcfb5dd907a8 #v5.0.0
      with:
        persist-credentials: false
    - uses: actions/setup-python@e797f83bcb11b83ae66e0230d6156d7c80228e7c #v6.0.0
    - uses: esacteksab/pre-commit-action@4563f702b4348f0109ef6a450d90f917067815e3 #v0.1.0
```

This does a few things:

- clones the code
- installs python
- sets up the `pre-commit` cache

### using this action with custom invocations

By default, this action runs all the hooks against all the files. `extra_args` lets users specify a single hook id and/or options to pass to `pre-commit run`.

Here's a sample step configuration that only runs the `flake8` hook against all the files (use the template above except for the `pre-commit` action):

```yaml
    - uses: esacteksab/pre-commit-action@4563f702b4348f0109ef6a450d90f917067815e3 #v0.1.0
      with:
        extra_args: flake8 --all-files
```

### using this action in private repositories

prior to v3.0.0, this action had custom behaviour which pushed changes back to
the pull request when supplied with a `token`.

this behaviour was removed:

- it required a PAT (didn't work with short-lived `GITHUB_TOKEN`)
- properly hiding this `input` from the installation and execution of hooks
  is intractable in github actions (it is readily available as `$INPUT_TOKEN`)
- this meant potentially unvetted code could access the token via the
  environment

you can _likely_ achieve the same thing with an external action such as
[git-auto-commit-action] though you may want to take precautions to clear `git`
hooks or other ways that arbitrary code execution can occur when running
`git commit` / `git push` (for example [core.fsmonitor]).

while unrelated to this action, [pre-commit.ci] avoids these problems by
installing and executing isolated from the short-lived repository-scoped
[installation access token].

[git-auto-commit-action]: https://github.com/stefanzweifel/git-auto-commit-action
[core.fsmonitor]: https://github.blog/2022-04-12-git-security-vulnerability-announced/
[pre-commit.ci]: https://pre-commit.ci
[installation access token]: https://docs.github.com/en/rest/apps/apps#create-an-installation-access-token-for-an-app
