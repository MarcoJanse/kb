# KB Linux Ubuntu cheat sheet

- [KB Linux Ubuntu cheat sheet](#kb-linux-ubuntu-cheat-sheet)
  - [Check Ubuntu version](#check-ubuntu-version)
  - [Package management](#package-management)
  - [apt vs apt-get commands](#apt-vs-apt-get-commands)
    - [New apt commands that don't exist in apt-get](#new-apt-commands-that-dont-exist-in-apt-get)
  - [Pinning packages](#pinning-packages)

## Check Ubuntu version

To check the Ubuntu version:

`lsb_release -a`

## Package management

- As usual you need to fetch an updated index from the Internet:
  - `$ sudo apt-get update`
- Check upgradable packages:
  - `$ sudo apt list --upgradable`
- To only upgrade package if installed:
  - `$ sudo apt-get --only-upgrade install Package`
- To install and or update single package:
  - `$ sudo apt-get install package`

## apt vs apt-get commands

Although apt commands replace commonly used apt-get and apt-cache functions, they are not backward compatible with all of them. You cannot always replace the older package managers with apt.

In the table below, see the apt command for any given function, as well as which command it replaces

| Command Function                                     | Existing Command                | apt Command                   |
|------------------------------------------------------|---------------------------------|-------------------------------|
| Update the package repository                        | apt-get update                  | apt update                    |
| Upgrade packages                                     | apt-get upgrade                 | apt upgrade                   |
| Upgrade packages and remove unnecessary dependencies | apt-get dist-upgrade            | apt full-upgrade              |
| Install a package                                    | apt-get install [package_name]  | apt install [package_name]    |
| Remove a package                                     | apt-get remove [package_name]   | apt-remove [package_name]     |
| Remove a package with configuration                  | apt-get purge [package_name]    | apt purge [package_name]      |
| Remove unnecessary dependencies                      | apt-get autoremove              | apt autoremove                |
| Search for a package                                 | apt-get search [package_name]   | apt-get search [package_name] |
| Show package information                             | apt-cache show [package_name]   | apt show [package_name]       |
| Show active package sources                          | apt-cache policy                | apt policy                    |
| Show installed and available versions of a package   | apt-cache policy [package_name] | apt policy [package_name]     |

### New apt commands that don't exist in apt-get

| Command Function          | New apt Command  |
|---------------------------|------------------|
| List packages by criteria | apt list         |
| Edit sources list         | apt edit-sources |

## Pinning packages

If you want to pin a package to a specific version, you could do so as follows.
For example, if you want to pin azure-cli to 2.36 but want to allow minor upgrades (like 2.36.2), do this:

- Create a file for azure-cli in
  - `sudo vim /etc/apt/preferences.d/azure.cli`
- Add the following lines:

```bash
Package: azure-cli
Pin: version 2.36.*
Pin-Priority: 999
```
