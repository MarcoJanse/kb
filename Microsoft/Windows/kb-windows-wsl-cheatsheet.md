# WSL cheat sheet

- [WSL cheat sheet](#wsl-cheat-sheet)
  - [WSL command line](#wsl-command-line)
  - [Using SSH on WSL2 and KeePassXC using SSH agent](#using-ssh-on-wsl2-and-keepassxc-using-ssh-agent)
    - [Prerequisites](#prerequisites)
      - [Windows VM](#windows-vm)
        - [WSL distro](#wsl-distro)
    - [Working with GIT and GitLab repositories using SSH authentication with KeePassXC and SSH agent](#working-with-git-and-gitlab-repositories-using-ssh-authentication-with-keepassxc-and-ssh-agent)
      - [Links](#links)

## WSL command line

- Enter WSL session from cmd.exe or PowerShell
  - `wsl`
- Shutdown WSL
  - `wsl --shutdown`

## Using SSH on WSL2 and KeePassXC using SSH agent

### Prerequisites

#### Windows VM

- WSL2 distro has been created (Ubuntu 2x.xx)
- OpenSSH and OpenSSL has been installed using Chocolatey or WinGet
- Enable SSH agent integration in KeePassXC
- Enable the OpenSSH authentication agent service in Windows and set it to automatic startup
- Save the private key file in KeePassXC
- Still in the private key file entry go to SSH Agent tab:
  - Make sure that automatic adding of the key when database is opened/unlocked is unchecked and removing the key when database is locked is checked in the SSH agent settings
- Create SSH key pair in Windows using openssl

##### WSL distro

- Make sure distro is up-to-date
- The npiperelay has been downloaded in `/home/username/npiperelay`
- Socat has been installed
  - `sudo apt install socat`
- Put the following in ~/.bashrc:

     ```bash
     export SSH_AUTH_SOCK=$HOME/.ssh/agent.sock

    ss -a | grep -q $SSH_AUTH_SOCK
    if [ $? -ne 0 ]; then
        rm -f $SSH_AUTH_SOCK
        (setsid socat UNIX-LISTEN:$SSH_AUTH_SOCK,fork EXEC:"$HOME/npiperelay/npiperelay.exe -ei -s //./pipe/openssh-ssh-agent",nofork &) >/dev/null 2>&1
    fi
    ```

- Restart bash process or log off and on again

### Working with GIT and GitLab repositories using SSH authentication with KeePassXC and SSH agent

- Open/Unlock KeePassXC database containing private key
- Select private key entry and select Add Key to SSH Agent or press [Ctrl]+[H]
- From within WSL, check if the SSH key is loaded using:
  - `ssh-add -L`
- This should list one entry, the private key you just added to KeePassXC
- Test connectivity to Gitlab using the private key:
  - `ssh -T git@gitlab.com` 
- This should output something like this:
- > Welcome to GitLab, @mjanse!
- If you don't get similar output, troubleshoot with this:
  - `ssh -Tv git@gitlab.com`
- This will give you Verbose output
- Finally, go to the directory you want to clone your GitLab repo to, or create one. For example:
  - `mkdir /home/arcom/git/gitlab/mjanse`
- From within the directory:
  - `git clone git@gitlab.com:mjanse/pblinfrastructure-fork.git`
- If successful, you should now have the PBL Infrastructure repository cloned to your WSL instance using private key provided from SSH Agent!

#### Links

- [How to use KeepassXC to serve SSH keys to WSL2 and Ubuntu](https://code.mendhak.com/wsl2-keepassxc-ssh/)
- [GitLab: Generate an SSH key pair](https://docs.gitlab.com/ee/ssh/#generate-an-ssh-key-pair)
- [GitHub: npiperelay](https://github.com/jstarks/npiperelay)