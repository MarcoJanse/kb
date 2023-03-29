# Git: set different user settings conditionally

## Introduction

I use git both personally, using my own private Github email address, but also for work, where I mostly use my work e-mail address for commits to Azure DevOps.

I want an easy way to sign my private commits using my private email address and work-related with my work email addres.
Here's how I did that

## Use an easy folder structure

These steps work best if you have different root directories for work related git repositories and private/public git repositories.
In my case, I only use GitHub privately and Azure DevOps for work-related repo's, so that's easy.

Therefore, my Git folder structure looks like this on a Windows laptop:

- C:\Git
  - AzDevOps
    - CustomerA
      - Repo1
      - Repo2
    - CostomerB
      - Repo1
  - GitHub
    - MarcoJanse
      - Repo1
      - Repo2
    - `<ExampleRepoName>`

## Set up

Because I primarily use non-work related git repositories, I user my GitHub user name and email address in my global config

### Basic commands

Some useful commands for viewing and setting config

- To view all config settings from a git repository
  - `git config --list`
- To view settings defined in your global config
  - `git config --global --list`
- To show where all the configuration is coming from in a git repository:
  - `git config --list --show-origin`
- To set your global user name
  - `git config --global user.name John Doe`
- To set your global email address
  - `git config --global user-email john.doe@privatemail.com`
- To view your configured email adres from within a git repository
  - `git config --get user.email`

### Creating the conditional includes file

First, I create a file called `.gitconfig_work` and store this in my documents folder for safe keeping.
You can name it whatever you want, but make sure it starts with a dot `(.)`.
I created a Git folder in my OneDrive folder for this.

In there, put the settings that apply only for your work-related repos.

```bash
[user]
	name = John Doe
	email = john.doe@company.com
```

### Add the conditional include to your global git configuration

Edit your global git config file via a code editor like VSCode. Mine is stored in `C:\Users\<username>\.gitconfig`

Add the include at the end, to make sure the settings don't get overwritten again later in the same file.

```bash
[user]
    name = John Doe
    email = john.doe@company.com
    signingkey = FAKESIGNINGKEY001
[gpg]
    program = c:/Program Files (x86)/GnuPG/bin/gpg.exe
[commit]
    gpgsign = true
[includeIf "gitdir/i:C:/Git/AzDevOps/"]
    path = ~/OneDrive - CompanyName/Documents/Git/.gitconfig_work
```

### Test the settings

- Open a new shell and test your settings from within a git repository in AzDevOps folder:
  - `git config --get user.email`
