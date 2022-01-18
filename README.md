# workflow-templates
Stores ReUsable workflows, composite actions and any items that can be called upon during deployment. For more information on re-usable components, read here: https://docs.github.com/en/actions/using-workflows/reusing-workflows.

## Publishing Nuget Packages
To publish nuget packages, the `publish-nuget-package.yml` file can be called. This Nuget does the following;
- Calls the checkout action setup using actions/checkout@v2
- Sets up dotnet using actions/setup-dotnet@v1. Versions 3.1.X and 6.0.X are used in this case.
- Prepares packages: This step adds a private nuget package source among the sources to be used.
- DotNet Restore: The `dotnet restore` step restores any packages needed.
- Build: calls the dotnet build step
- Test: calls the `dotnet test` to run any unit tests in the project/solution. 
- Package: Calls the `dotnet pack` command. The *proj_or_sln_path* specified is used here. Configuration used is Release.
- Publish: Calls the `dotnet nuget push` command. The *NUGET_API_KEY* secret and *nuget_source_path* are used to push and publish the nuget package. 

The input to be specified include:
- **proj_or_sln_path**: The project solution path to be used
- **nupkg_root_path**: The path to the nuget package that is generated
- **nuget_source_path**: The source URL of the nuget source where the package will be published. **Please note that the source should be set in double quotes**

Secrets to be specified include:
- **NUGET_USERNAME**: The Nuget username used for authenticating to a private nuget source that is to be added to download any private libraries.
- **NUGET_API_KEY**: The Nuget key specified for downloading any private libraries and during publishing.

The example below shows how this workflow can be reused;
```YAML
name: Publish to nuget
on:
  push:
    branches:
      - master

jobs:
  publish-nuget-package:
    uses: emata-ug/workflow-templates/.github/workflows/publish-nuget-package.yml@v1.0.0
    with:
      proj_or_sln_path: [YOUR_PROJ_OR_SLN]
      nupkg_root_path: [YOUR_NUPKG_ROOT_PATH]
      nuget_source_path: "[YOUR_NUGET_SOURCE_PATH]" 
      
    secrets:
      NUGET_API_KEY: ${{ secrets.[YOUR_SECRET_NUGET_API_KEY] }}
      NUGET_USERNAME: ${{ secrets.[YOUR_SECRET_NUGET_USERNAME] }}
```
> Values denoted in Square brackets [YOUR_*] represent the custom implementation.
