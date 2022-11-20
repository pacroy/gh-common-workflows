# Common GitHub Actions Workflows

This repository contains GitHub Actions workflows boilerplate that you want to sync to other repositories. This saves your time so you only make changes to common workflow YAMLs in one place without doing it in each repository one by one.

This works by creating a new GitHub Actions workflow in your target repositories and it will automatically sync files or folders from this repository. The workflow can be customized so it only overwrites or deletes specific files and folders.

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

1. Generate a new Personal Access Token with proper permissions as follow:
  
   - For classic token, choose `repo` and `workflow`.
   - For fine-grained token, choose the following:
   
    ```properties
    Repository:Contents=Read and write
    Repository:Metadata=Read-only
    Repository:Secrets=Read and write
    Repository:Workflows=Read and write
    ```

2. Manually run the workflow [Sync Secrets](https://github.com/pacroy/gh-common-workflows/actions/workflows/_sync_secrets.yml) and input your target repository.

    ![CleanShot 2022-06-30 at 20 09 59](https://user-images.githubusercontent.com/24604485/176685449-dc9e6ff1-df29-4db6-92a8-fa820ff7edc9.png)

3. In your target reposotory, create a new workflow `.github/workflows/sync.yml` copy the content from [sync.yml](.github/workflows/sync.yml).

4. Get the workflow runs and it will automatically sync files and folders which you can customize in [sync.yml](.github/workflows/sync.yml)
