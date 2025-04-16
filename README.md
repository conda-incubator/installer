# Conda Installer

This action creates [constructor](https://github.com/conda/constructor)-based installers.
It creates a `conda` or `mamba` environment based on a supplied `environment.yaml` file
and runs `constructor` to create the installer. It outputs the directory containing the build
artifacts.

For all available input parameters, see the [`action.yaml`](action.yaml) file.
For full usage examples, see the [test workflows](.github/workflows).

## Usage

### Basic usage

```yaml
- name: Build installer
  id: build
  uses: conda-incubator/installer
  with:
    environment-yaml-file: environment.yaml
    recipe-directory: recipe
- name: Check output
  run:
    ls -la "${{ steps.build.outputs.artifacts-directory }}"
```

The action requires two input parameters:

* `environment-yaml-file`: The file containing channels, dependencies, and environment variables
                           for the installer build environment. `constructor` must be part of the
                           dependency list.
                           The content of that file can also be
                           [supplied as a string](#using-a-string-instead-of-an-environment-file).
* `recipe-directory`: The directory containing the `construct.yaml` file.

The output of the action is the directory containing the build artifacts, i.e., the installers and
other output files requested in the `construct.yaml`.

### Using a string instead of an environment file

The content of an `environment.yaml` file can also be input directly into the action.
This is useful for using expressions to conditionally set the content of the file as opposed
to having separate YAML files.

```yaml
- uses: conda-incubator/installer
  with:
    environment-yaml-string: |
      channels:
        - conda-forge
      dependencies:
        - constructor
      variables:
        EXT: ${{ runner.os == 'Windows' && 'exe' || 'sh' }}
    recipe-directory: recipe
```

> [!NOTE]
> The `environment-yaml-file` input takes precedence over `environment-yaml-string`.

### Specifying the conda/mamba location

If the `conda`/`mamba` are installed, but not in `PATH`, the location of the installation
directory to create the build environment can be specified directly.

```yaml
- uses: conda-incubator/installer
  with:
    conda-root: ${{ env.CONDA }}
    recipe-directory: recipe
```

### Using a different bootstrapper

By default, `constructor` uses `conda-standalone` installed into the build environment.
A different binary can be supplied via the `--conda-exe` input flag.
The location to that binary can be set via the `standalone-location` input.


```yaml
- uses: conda-incubator/installer
  with:
    recipe-directory: recipe
    standalone-location: ${{ runner.temp }}/mamba/micromamba
```

The standalone executable is also used to create the build environment if `conda`/`mamba` is not
in `PATH` and `conda-root` is not set.

### Building inside a container

> [!NOTE]
> This feature is only supported using Docker on Linux.

Installers can be build inside a container.

```yaml
- uses: conda-incubator/installer
  with:
    environment-yaml-file: environment.yaml
    container-arch: linux/arm64
    container-image: condaforge/linux-anvil-aarch64
    recipe-directory: recipe
```

The container must contain an instance of `conda` or `mamba`.
The location can be supplied in three different ways:

1. By having `conda`/`mamba` available via the `PATH` environment variable.
1. By setting `conda-root` to the installation directory inside the image.
1. Via the `standalone-location` input. The input refers to the location of the standalone binary
   at the host. The directory will be mounted into the container.

### Signing Windows installers

Signing Windows installers with `constructor` requires secrets.
To avoid writing secrets into an `environment.yaml` file, these secrets can be input directly.
For a full list of supported secrets, see the [`action.yaml`](action.yaml) file.

```yaml
- uses: conda-incubator/installer
  with:
    constructor-pfx-certificate-password: ${{ secrets.pfx-password }}
    environment-yaml-string: |
      channels:
        - conda-forge
      dependencies:
        - constructor
      variables:
          CONSTRUCTOR_SIGNTOOL_PATH: C:\Program Files (x86)\Windows Kits\10\bin\10.0.17763.0\x86\signtool.exe
    recipe-dir: recipe
```

## Build status

| Workflow Status                                              |
| ------------------------------------------------------------ |
| [![Test container builds][ex-container-badge]][ex-container] |
| [![Test defaults][ex-defaults-badge]][ex-defaults]           |
| [![Test from environment.yaml file][ex-file-badge]][ex-file] |
| [![Test Micromamba][ex-micromamba-badge]][ex-micromamba]     |
| [![Test signing installers][ex-signing-badge]][ex-signing]   |
| [![Test expected failures][ex-failures-badge]][ex-failures]  |

[ex-container]:
  https://github.com/conda-incubator/installer/actions/workflows/test-container.yaml
[ex-container-badge]:
  https://github.com/conda-incubator/installer/actions/workflows/test-container.yaml/badge.svg?branch=main
[ex-defaults]:
  https://github.com/conda-incubator/installer/actions/workflows/test-defaults.yaml
[ex-defaults-badge]:
  https://github.com/conda-incubator/installer/actions/workflows/test-defaults.yaml/badge.svg?branch=main
[ex-failure]:
  https://github.com/conda-incubator/installer/actions/workflows/test-failure.yaml
[ex-failure-badge]:
  https://github.com/conda-incubator/installer/actions/workflows/test-failure.yaml/badge.svg?branch=main
[ex-file]:
  https://github.com/conda-incubator/installer/actions/workflows/test-file.yaml
[ex-file-badge]:
  https://github.com/conda-incubator/installer/actions/workflows/test-file.yaml/badge.svg?branch=main
[ex-micromamba]:
  https://github.com/conda-incubator/installer/actions/workflows/test-micromamba.yaml
[ex-micromamba-badge]:
  https://github.com/conda-incubator/installer/actions/workflows/test-micromamba.yaml/badge.svg?branch=main
[ex-signing]:
  https://github.com/conda-incubator/installer/actions/workflows/test-signing.yaml
[ex-signing-badge]:
  https://github.com/conda-incubator/installer/actions/workflows/test-signing.yaml/badge.svg?branch=main

## Team charter

The conda team to work on tools, techniques and documentation about conda installers such as miniconda, miniforge etc.

The team charter is dynamic following the conda governance policy, with the following caveats:

- 🧰 The team’s purpose is to develop and maintain code included in the conda/installer repository. That repository contains among other things tools and scripts to make it incredibly easy to create and maintain installers similar to the current “miniconda” or “miniforge” installers.

- 📨 The team acknowledges the need for better coordination between stakeholders that work on installers and bootstrappers in the conda community.

- 📢 To enable accountability and predictability towards end users, the team commits to open and transparent communication about the release process, whenever possible.

- ♻️ The team will abstract code shared between the various installers in the ecosystem in a central location to reduce diverging implementations.

### Areas of work

- Adopt various installer automation from Anaconda and the conda community to centralize maintenance

- Review overlap with the existing miniforge repo together with miniforge maintainers

- Apply sensible measures to prevent uncoordinated changes, e.g. branch protection, required code review by at least two maintainers via CODEOWNERS or 4, 6 or 8 eyes review principle

- Add reusable “composite GitHub Action” (like conda’s CLA action) to installer directory with parameters for main config variables, e.g. signing keys, to conda installer repo for use by 1st and 3rd parties

- Make use of created action in:
  - A new repo called conda/miniconda (?) that automates the build of miniconda (for handover to Anaconda/community QA)
  - A new Anaconda-internal (!) repo that automates the creation of the Anaconda Distribution Installer in a similar way

- Design decisions on how installer creation should split off between the various stakeholders involved

- Supply chain security topics such as reproducibilty and code signing

- Opportunities of tying together various “bundling” tools under a new `conda bundle` subcommand?

- ...

### Members

#### conda & constructor maintainers

- @jezdez
- @chenghlee
- @jaimergp

#### miniforge maintainers

- @xhochy
- @isuruf (also constructor maintainer)

#### Anaconda packaging

- @AndrewVallette
- @marcoesters
- @psteyer
- @pseudoyim (also constructor maintainer)

#### Anaconda infra

- @dbast

#### Anaconda security

- @awwad
- @pkmooreanaconda

### Anaconda custom services

- @jlstevens
