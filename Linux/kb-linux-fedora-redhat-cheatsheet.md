# KB Linux Fedora/Red Hat cheat sheet

- [KB Linux Fedora/Red Hat cheat sheet](#kb-linux-fedorared-hat-cheat-sheet)
  - [RDP](#rdp)
  - [Manual package install](#manual-package-install)
  - [Kernel cleanup](#kernel-cleanup)
  - [DNF package manager](#dnf-package-manager)

## RDP

Config files:

- xrdp
- sesman.ini

## Manual package install

- Download via wget
  - `wget https://kojipkgs.fedoraproject.org//packages/log4j/2.15.0/1.fc35/noarch/log4j-2.15.0-1.fc35.noarch.rpm`
- Install with dnf
  - `sudo dnf localinstall log4j-2.15.0-1.fc35.noarch.rpm`

## Kernel cleanup

- Display current version of Linux and note it down
  - `sudo uname -sr`
- List all installed kernels on system
  - `sudo rpm -q kernel-core`
- Uninstall old unneeded kernels
  - `sudo dnf remove kernel-core-x.xx.xx-xxx.fc35.x86_64`
- Limit the amount of kernels to keep:
  - `sudo vim /etc/dnf/dfn.conf`
- Change the following entry to the desired value, for example 2:
  - `installonly_limit=2`

## DNF package manager

- List installed packages
  - `dnf list installed`
- Show info about a specific package
  - `dnf info <package_name>`
- Find a package and it's source
  - `dnf search <package_name>`
- Find which package contains a package, value
  - `dnf provides package_name`
  - `dnf provides ifconfig`
  - `dnf provides telnet`
- Install a downloaded package locally
  - `dnf localinstall <your_package_name>.rpm`
- Reinstall a package
  - `dnf reinstall package_name`
- Check for updates
  - `dnf check update`
- List all updates
  - `dnf list updates`
- Install updates
  - `dnf update`
- Update specific package
  - `dnf update <package_name>`
- Update conflicting packages
  - `sudo dnf update --best --allowerasing`
- Downgrade or upgrade all packages to latest based on repo's
  - `dnf distro-sync`
- Remove package
  - `dnf remove <package_name>` 
- List enabled repositories
  - `dnf repolist`
- List all reposotories, including disabled ones
  - `dnf repolist all`
- Enable a repository
  - `dnf config-manager --set-enabled <repository>`
- Disable a repository
  - `dnf config-manager --set-disabled repository`
- List package groups
  - `dnf grouplist`
- Install a group with all packages
  - `dnf groupinstall <group_name>`
- Remove a group and all packages
  - `dnf groupremove <group_name>`
- Remove temporary files
  - `dnf clean all`
- Remove cache files for repo metadata
  - `dnf clean dbcache`
- Remove local cookie files (time signatures)
  - `dnf clean expire-cache`
- Remove repo metadata
  - `dnf clean metadata`
- Removes cached packages
  - `dnf clean packages`
- Autoremove no longer used packages (old dependencies)
  - `dnf autoremove`
- View dnf history
  - `dnf history`
- Get details about a specific history id
  - `dnf history info <id_number>`
