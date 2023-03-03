# Reusable workflows #

The following workflows have been created in order to simplify and standardize CI/CD GitHub Actions

- git-versioning
- dotnet-core-build
- dotnet-core-docker-build
- dotnet-framework-build
- deploy-octopus

## git-versioning
Calculates a sematic git version number for the attached repository.
### Usage
```   
versioning:
    uses: ardevora/reusableactions/.github/workflows/git-versioning.yml@main
```
### Inputs
None
### Outputs
#### **semver**
Workflow sematic version number.
#### **semver_lowercase**
workflow semantic version number lowercased.

## dotnet-core-build
Builds a .net solution, runs any tests and publishes to a nuget store.
### Usage
```
  versioning:
    uses: ardevora/reusableactions/.github/workflows/git-versioning.yml@main

  build:
    needs: versioning
    uses: ardevora/reusableactions/.github/workflows/dotnet-core-build.yml@main
    with:
      semver: ${{ needs.versioning.outputs.semver }}
      dotnet_version: 7.0.x
      source_folder: "src"
    secrets: inherit
```
### Inputs
#### **semver**
Target version for the nuget package. [string] [required]
#### **dotnet_version**
Target build version for the dotnet packages. [string] [required]
#### **source_folder**
Relative folder for the source files. [string] [required]
#### **gh_packages_url** [default: https://nuget.pkg.github.com/ardevora/index.json]
Target nuget store to publish the packages to. [string]
#### **run_tests** [default: true]
Option to run the tests [boolean]
#### **push_to_package_store** [default: false]
Option to push the packages to the package store. [boolean]
### Outputs
None
### Secrets
#### **GHA_TOKEN** [required]

## dotnet-core-docker-build
Build and packages a docker image and publishes to the github container registry
### Usage
```
  versioning:
    uses: ardevora/reusableactions/.github/workflows/git-versioning.yml@main

  build:
    needs: versioning
    uses: ardevora/reusableactions/.github/workflows/dotnet-core-docker-build.yml@main
    with:
      semver_lowercase: ${{ needs.versioning.outputs.semver_lowercase}}
      source_folder: "src"
      package_name: "ardevora.package.api"
      docker_project: "Ardevora.Package.WebApi"
    secrets: inherit
```
### Inputs
#### **semver_lowercase**
Target version for the docker image

#### **source_folder**
Relative folder for the source files. [string] [required]
#### **package_name**
Package name for the docker image
#### **docker_project**
Name of the docker project to be built
### Outputs
None
### Secrets
#### **GHCR_TOKEN** [required]
#### **GHA_USERNAME** [required]
#### **GHA_TOKEN** [required]

## dotnet-framework-build
Builds and packages a dotnet framework solution and publishes to a package store.
### Usage
```
  versioning:
    uses: ardevora/reusableactions/.github/workflows/git-versioning.yml@main

  build:
    needs: versioning
    uses: ardevora/reusableactions/.github/workflows/dotnet-framework-build.yml@main
    with:
      semver: ${{ needs.versioning.outputs.semver }}
      source_folder: "src"
      solution_name: "UnifiedFileProcessor"
      package_name: "ardevora.ops.ufp"
      artifact_root: "artifacts"
    secrets: inherit
```
### Inputs
#### **semver**
Target version for the nuget package. [string] [required]
#### **source_folder**
Relative folder for the source files. [string] [required]
#### **solution_name**
Name of the solution to be built. [string] [required]
#### **package_name**
Name of the package to be built. [string] [required]
#### **artifact_root**
Relative folder for the artifact to be generated in. [string] [required]
#### **gh_packages_url** [default: https://nuget.pkg.github.com/ardevora/index.json]
Target nuget store to publish the packages to. [string]
#### **format** [default: Zip]
The format to use when packing the output [string] [Zip or NuPkg]
#### **push_to_package_store** [default: false]
Option to push the packages to the package store. [boolean]
### Outputs
None
### Secrets
#### **GHA_USERNAME** [required]
#### **GHA_TOKEN** [required]

## deploy-octopus
Pushes packages to Octopus
### Usage
```
  versioning:
    uses: ardevora/reusableactions/.github/workflows/git-versioning.yml@main

  push:
    needs: versioning
    uses: ardevora/reusableactions/.github/workflows/push-octopus.yml@main
    with:
      semver: ${{ needs.versioning.outputs.semver }}
      source_folder: "src"
      artifact_root: "artifacts"
      package_name: "ardevora.ops.ufp"
    secrets: inherit
```
### Inputs
#### **semver**
Target version for the nuget package. [string] [required]
#### **source_folder**
Relative folder for the source files. [string] [required]
#### **artifact_root**
Relative folder for the artifact to be generated in. [string] [required]
#### **package_name**
Name of the package to be built. [string] [required]
#### **octopus_space** [default: default]
The octopus space to deploy to . [string]
#### **create_release** [default: false]
Option to create a release in Octopus.
#### **octopus_project**
The name of the octopus project to create a release in. [string] [required if create_release is true]
### Outputs
None
### Secrets
#### **OCTOPUS_APIKEY** [required]
#### **OCTOPUS_SERVER_URL** [required]