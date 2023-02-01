---
layout: page
title: Start Here
---

## Installing Git

This workshop will generally assume that you are using a Linux terminal, but everything should work under Windows as well if you substitute appropriate shell commands.

### Ubuntu

If Git is not already installed, use the following command to install it:

```shell
sudo apt update && sudo apt install git
```

If you want the latest and greatest Git features you can also install Git from a [PPA](https://launchpad.net/~git-core/+archive/ubuntu/ppa).

### Windows

#### Git for Windows

Download and run the installer from the [Git for Windows](https://gitforwindows.org/) website.

#### Chocolatey

[Chocolatey](https://chocolatey.org/) is a package manager for Windows based on the NuGet package format.

First follow the instructions found [here](https://chocolatey.org/install) to install Chocolatey for individual use. Then run the following command in an administrator command prompt:

```shell
choco install -y git
```

## Configuring Git

Before using Git, you need to configure at least your name and email address.

```bash
# Configure your name and email address
git config --global user.name "John Doe"
git config --global user.email john.doe@gmail.com
```

I recommend setting the following options as well:

```bash
# Disable Git's automatic line ending conversion (LF on Linux, CRLF on Windows)
# I prefer to use .gitattributes to control line endings instead
git config --global core.autocrlf false

# Prefer a rebase instead of a merge commit when pulling a branch
git config --global pull.rebase true

# Some optional but convenient aliases
git config --global alias.gr "log --oneline --graph --decorate --all"
git config --global alias.fe "fetch -pt"
git config --global alias.br "branch"
git config --global alias.sup "submodule update --init --recursive"
```

Now you should be ready to start with [Module 1][1]!

{% capture module1_url %}
  {%- post_url 2022-09-07-01-gitting-started -%}
{% endcapture %}
[1]: {{ module1_url | relative_url }}
