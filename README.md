# git-workshop

Hands-on Git workshop to practice your skills and Git good!

## Modules

* [Module 1: Gitting Started](modules/01-gitting-started/README.md)
* [Module 2: Feature Branch Workflow](modules/02-feature-branch-workflow/README.md)
* [Module 3: Stacking Feature Branches](modules/03-stacking-feature-branches/README.md)

## Installing Git

### Ubuntu

If Git is not already installed, use the following command to install it:

```shell
sudo apt update && sudo apt install git
```

### Windows

#### Git for Windows

Download and run the installer from the [Git for Windows](https://gitforwindows.org/) website.

#### Chocolatey

Chocolatey is a package manager for Windows based on the NuGet package format.

First follow the instructions found [here](https://chocolatey.org/install) to install Chocolatey for
individual use. Then run the following command in an administrator command prompt:

```shell
choco install -y git
```

## Basic Configuratoin

```bash
# Configure your name and email address
git config --global user.name "John Doe"
git config --global user.email john.doe@gmail.com

# Disable Git's automatic line ending conversion
# Recommended to use .gitattributes instead
git config --global core.autocrlf false

# Prefer a rebase instead of a merge commit when pulling a branch
git config --global pull.rebase true
```

Now you should be ready to start with [Module 1](modules/01-gitting-started/README.md)!
