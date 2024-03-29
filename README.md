# Deploy to Remote Repository Action

Uses rsync and git to deploy files/folders from a local GitHub action repository
to a remote repository.

_Notes_:

- We do not leverage external actions to manage the SSH agent as we want to keep
  the code as simple/single-sourced as possible.
- We must manually manage the SSH keys for the remote repository. This is
  typically done by adding the private key to the GitHub action secrets and then
  adding the public key to the remote repository (eg. as a write deploy key).

## Usage

Example deploy to a remote repository:

```yml
name: Deploy to Pantheon Live

on:
  push:
    branches:
      - develop

jobs:
  build-and-sync:
    runs-on: ubuntu-latest
    steps:
    - name: Checkout code
      uses: actions/checkout@v3

    - name: Sync to Remote Repository
      uses: padillaco/action-deploy-to-remote-repository@main
      with:
        remote_repo: 'ssh://user@server/example.git'
        remote_branch: 'master' # Notable that this differs from 'production'
        destination_directory: '/'
        exclude_list: '.git, .github, .gitmodules, node_modules'
        ssh_key: ${{ secrets.REMOTE_REPO_SSH_KEY }}
```

### `.deployignore`

The action supports recursively replacing all `.gitignore` files in your project
with a root-level `.deployignore` file if one is found. This is useful for
excluding files from the deployment that would have previously been ignored by
version control (such as built assets and Composer dependencies). The
`.deployignore` syntax is the same as a normal `.gitignore` file.

## Inputs

> Specify using `with` keyword.

### `os`

- Specify the operation system to use.
- Accepts a string.
- Defaults to `ubuntu-latest`.

### `remote_repo`

- Specify the remote repository to deploy to.
- Accepts a string.
- Required.

### `remote_branch`

- Specify the remote branch to deploy to.
- Accepts a string.
- Defaults to the same branch name in the remote repo as the current running
  action.

### `base_directory`

- Specify the base directory to sync from.
- Accepts a string.
- Defaults to the root of the repository (`.`). **NOTE** You likely want a
  trailing slash if you're syncing a subdirectory. (eg. `wp-content/`)

### `destination_directory`

- Specify the destination directory to sync to.
- Accepts a string.
- Defaults to the root of the remote repository (`.`).

### `exclude_list`

- Specify a comma-separated list of files and directories to exclude from sync.
- Accepts a string. (e.g. `.git, .gitmodules`)
- Defaults to `.git, .gitmodules`.

### `ssh_key`

- Specify the SSH key to use for the remote repository (requires write access).
- Required.

## Changelog

Please see [CHANGELOG](CHANGELOG.md) for more information on what has changed
recently.

## Credits

This project was created by [Alley Interactive](https://github.com/alleyinteractive).

- [Ben Bolton](https://github.com/benpbolton)
- [All Contributors](https://github.com/alleyinteractive/action-deploy-to-remote-repository/graphs/contributors)

## License

The GNU General Public License (GPL) license. Please see [License File](LICENSE)
for more information.
