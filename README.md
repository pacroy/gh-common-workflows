# Common GitHub Actions Workflows

This repository contains GitHub Actions workflows boilerplate that will be used in other repositories. The benefit of this is we save time by maintaining the workflows in only one place here and they will be synced to target repositories you want.

This works by setting up the [Sync Files workflow](.github/workflows/sync.yml) in your target repositories and it will automatically sync files or folders you want to your target repositories. The workflow will overwrite files that exist in this repositories and leave other files untouched so you can still have custom workflows or configurations in yout target repositories.
