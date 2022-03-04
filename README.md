# Homeserver Ansible playbook 

This is the Ansible playbook I use to automate installation and configuration of the services on my homeserver.
Do note that this is for personal use, so do not rely on this repo for anything important.

The playbook assumes you have Arch Linux ARM installed on a machine in your LAN, connected to ethernet.
It should have default credentials (alarm - alarm, root - root). Installation of Python and use of pacman-key is handled.

Special thanks to [Wolfgang](https://github.com/notthebee/) for the idea of automating the installation process.
This project was largely inspired by his own [infra](https://github.com/notthebee/infra) repo.

## Services

* Gitea
* Exim mailserver (for local use only)
* Matrix Synapse
* Nginx webserver
* MediaWiki farm
* SFTP (not really a service, included in sshd)
* Firewall (UFW)

## Miscellaneous features

* Bootstrapping Python
* Setting up a LAN static IP address (NetworkManager)
* Filesystem decryption and mounting
* Dotfile installation

## Usage

Install ansible. [Install guide](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html)


Clone the repo:
```
git clone https://github.com/dogeystamp/homeserver-ansible
```

Create a hosts file based on hosts.example:
```
cd homeserver-ansible
cp hosts.example hosts
vim hosts
```


Adjust group variables (remember this is for all your hosts):
```
vim group_vars/all/vars.yml
```


Adjust host variables:
```
mkdir -p host_vars/[hostname]/
vim host_vars/[hostname]/vars.yml
```

Create vault for secrets:
```
ansible-vault create host_vars/[hostname]/vault.yml
ansible-vault edit host_vars/[hostname]/vault.yml
```

A template for secret variables can be found near the end of `group_vars/all/vars.yml`


Add secret files:

```
# Keyfile for LUKS disk encryption
dd if=/dev/random of=roles/filesystems/files/k5e.secret bs=1024 count=2
ansible-vault encrypt roles/filesystems/files/k5e.secret

# This is a signing key for Matrix Synapse. It should be from a previous install.
ansible-vault encrypt roles/services/synapse/files/signing.key.secret
