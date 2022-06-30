# Common GitHub Actions Workflows

This repository contains GitHub Actions workflows boilerplate that you want to run in other repositories. The benefit of this is we save time by maintaining the workflow YAMLs in only one place and they will be synced to target repositories you want.

This works by setting up the [Sync Files workflow](.github/workflows/sync.yml) in your target repositories and it will automatically sync files or folders that you want. The workflow will overwrite only files that exist in the source repositories and leave other files untouched so you can still have custom workflows or configurations in yout target repositories.

```mermaid
  graph LR;
    source("Source repository")
    target1("Target repository 1")
    target2("Target repository 2")
    target3("Target repository 3")
    targetn("Target repository n")

    source --pull--> target1
    source --pull--> target2
    source --pull--> target3
    source --pull--> targetn
```

This sync process is designed to work well with [GitHub Flow](https://docs.github.com/en/get-started/quickstart/github-flow).

## Usage

1. Generate a new Personal Access Token with the permissions `repo` and `workflow`.

    ![CleanShot 2022-06-29 at 10 15 28](https://user-images.githubusercontent.com/24604485/176343541-0bc82c53-8aca-427a-b5b7-fab7d68feafc.png)

2. Manually run the workflow [Sync Secrets](https://github.com/pacroy/gh-common-workflows/actions/workflows/_sync_secrets.yml) and input your target repository.

    ![CleanShot 2022-06-30 at 20 09 59](https://user-images.githubusercontent.com/24604485/176685449-dc9e6ff1-df29-4db6-92a8-fa820ff7edc9.png)

3. In your target reposotory, create a new workflow `.github/workflows/sync.yml` copy the content from [sync.yml](.github/workflows/sync.yml).

4. Get the workflow runs and it will automatically sync files and folders which you can customize in [sync.yml](.github/workflows/sync.yml)
