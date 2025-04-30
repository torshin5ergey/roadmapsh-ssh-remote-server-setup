# 🔐 SSH Remote Server Setup Project for roadmap.sh

This is my solution to the [SSH Remote Server Setup project](https://roadmap.sh/projects/ssh-remote-server-setup) in the [DevOps roadmap](https://roadmap.sh/devops) from [roadmap.sh](https://roadmap.sh/)

## Project Requirements

- Register and setup a remote linux server on any provider.
- Create two new SSH key pairs and add them to your server.
- You should be able to connect to your server using both SSH keys.
- *Advanced*. Setup login with alias.
- *Advanced*. Install and configure `fail2ban` to prevent brute force attack.

## Prerequisites

- A remote host with OpenSSH-server installed.
- Remote server IP address.

## SSH Remote Server Setup Guide

1. Generate SSH key pairs on your local machine. Use ED25519 or RSA key type
```bash
# -t key type
# -b bits in the key
# -C comment
# -f output filename
ssh-keygen -t ed25519 -C "comment"
# or
ssh-keygen -t rsa -b 4096 -f ~/.ssh/id_rsa_kubernetes -C "comment"
```

2. Copy **public** key to your remote server. Ypu need to provide username's password for this action
```bash
# -i identity file (public key file)
ssh-copy-id -i path/to/public/key username@hostip
```

3. *Advanced.* Add host to `~/.ssh/config` to connect with `alias`
```bash
Host alias
  HostName hostip # IP od DNS from /etc/hosts
  User username
  Port 22 # optional SSH port (default is 22)
  IdentityFile path/to/private/key
```

4. SSH into your remote server
```bash
# -i identity file (private key file)
# -p SSH port

# with username and hostname
ssh -i path/to/private/key username@hostip

# with alias
ssh alias
```

5. *Advanced*. Install and Setup `fail2ban`

- Install `fail2ban`
```bash
sudo apt update && sudo apt install fail2ban -y
```
- Create **custom** configuration file from default `/etc/fail2ban/jail.conf`. Setup `fail2ban` parameters only inside custom configuration files. Default `maxretry=5`, `bantime=10m`.
```bash
sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.d/jail.local
```
- Start and enable service
```bash
sudo systemctl start fail2ban
sudo systemctl enable fail2ban
```
- Check ban status
```bash
sudo fail2ban-client status sshd
```

## Some SSH configuration Best Practices

`/etc/ssh/sshd_config`

- Disable password based login
```bash
AuthenticationMethods publickey
PubkeyAuthentication yes
```
- Disable login users with empty passwords
```bash
PermitEmptyPasswords no
```
- Disable root login
```bash
PermitRootLogin no
ChallengeResponseAuthentication no
PasswordAuthentication no
UsePAM no
```
- Allow access only from specific IPs
```bash
AllowUsers user@192.168.0.*
```
- Restart `sshd` after changing the configuration
```bash
sudo systemctl restart sshd
```

## Author

Sergey Torshin [@torshin5ergey](https://github.com/torshin5ergey)
