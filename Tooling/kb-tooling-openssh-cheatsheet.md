# OpenSSH cheat sheet

## Create RSA SSH key that's compatible with Azure

* Open PowerShell on a system with OpenSSH installed
  * `ssh-keygen -m PEM -t rsa -b 4096`

## Create and ED25519 SSH key

* Open PowerShell on a system with OpenSSH installed
  * `ssh-keygen -t ed25519`

## Key management using KeePassXC

* Save the private and public key as attachments in your SSH entry
* Use the SSH agent tab to link the private key