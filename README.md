# build-and-sign-monogame-android

This action allows you to build and sign your MonoGame Android project for release.

The action sets up `dotnet`, `msbuild`, and the `mgcb` build tool.
It then builds the `Content` resources using `mgcb`, then builds and signs the
game using `msbuild`, and finally uploads both the signed and unsigned `aab`s
as artifacts to the workflow.

If you're looking for an action that only builds your MonoGame project, check
out [build-monogame](https://github.com/marketplace/actions/build-monogame-project).

# Usage

```yml
name: Generate signed aab for release

on:
  pull_request:
    branches: [master]

  # allow running this workflow manually
  workflow_dispatch:

jobs:
  generate-signed-aab:
    runs-on: windows-latest

    steps:
      - name: Check out the repo
        uses: actions/checkout@v2

      - name: Build Android project and sign generated aab
        uses: igotinfected-ci/build-and-sign-monogame-android@v1
        with:
          solution-path: '${{ github.workspace }}\Project\Project.sln'
          content-mgcb-path: '${{ github.workspace }}\Project\Android\Content'
          project-path: '${{ github.workspace }}\Project\Android'
          csproj-path: '${{ github.workspace }}\Project\Android\Android.csproj'
          build-configuration: "Release"
          package-format: "aab"
          keystore: ${{ secrets.KEYSTORE }}
          keystore-password: ${{ secrets.KEYSTORE_PASSWORD }}
          key-alias: ${{ secrets.KEY_ALIAS }}
          key-password: ${{ secrets.KEY_PASSWORD }}
```

This action uploads the generated `aab`s as artifacts (`name: signed-aab`) to the workflow, meaning
you can then use [download-artifact](https://github.com/marketplace/actions/download-a-build-artifact)
to download them via a second job in the workflow.

Retrieving the uploaded artifact:

```yml
name: Generate signed aab for release and do something with it

on:
  pull_request:
    branches: [master]

  # allow running this workflow manually
  workflow_dispatch:

jobs:
  generate-signed-aab:
    runs-on: windows-latest

    steps:
      - name: Check out the repo
        uses: actions/checkout@v2

      - name: Build Android project and sign generated aab
        uses: igotinfected-ci/build-and-sign-monogame-android@v1
        with:
          solution-path: '${{ github.workspace }}\Project\Project.sln'
          content-mgcb-path: '${{ github.workspace }}\Project\Android\Content'
          project-path: '${{ github.workspace }}\Project\Android'
          csproj-path: '${{ github.workspace }}\Project\Android\Android.csproj'
          build-configuration: "Release"
          package-format: "aab"
          keystore: ${{ secrets.KEYSTORE }}
          keystore-password: ${{ secrets.KEYSTORE_PASSWORD }}
          key-alias: ${{ secrets.KEY_ALIAS }}
          key-password: ${{ secrets.KEY_PASSWORD }}

  do-something-with-it:
    runs-on: windows-latest

    steps:
      - name: Download artifact
        uses: actions/download-artifact@v2
        with:
          name: signed-aab
      # artifact in current working directory now as a zip file containing
      # both the unsigned and signed aabs
```

# License

The scripts and documentation in this project are released under the [MIT License](LICENSE)
