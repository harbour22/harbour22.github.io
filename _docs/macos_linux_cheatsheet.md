---
title: "MacOS/Linux Cheatsheet"
permalink: /docs/macos_linux_cheatsheet/
excerpt: "Useful hints and reminders"
toc: false
classes: wide
---

## Passwordless ssh

Assumption is we already have id_rsa.pub created with .ssh

```bash
ssh-copy-id -i id_rsa.pub remote_username@server_ip_address
```
Output should look something like

```bash
Number of key(s) added:        1

Now try logging into the machine, with:   "ssh 'remote_username@server_ip_address'"
and check to make sure that only the key(s) you wanted were added.
```

## Using multiple public/private keys

For example, with github and a key "github_rsa", add the following to the ~/.ssh/config file

(UseKeychain only applicable on osx machines)

```bash
Host github.com
 HostName github.com
 User git
 AddKeysToAgent yes
 UseKeychain yes
 IdentityFile ~/.ssh/github_rsa
```

## Creating ssh shortcuts

```bash
Host nano
 HostName <IP Address>
 User <Username>
```

Then it's possible to simply run

```bash
ssh nano
Welcome to Ubuntu 18.04.4 LTS (GNU/Linux 4.9.140-tegra aarch64)
```

## Starting a service on startup

Example given here for vscode-server.   Adding in support for a default password so we don't need to check the logs to see what was set
```bash
# sudo vi /etc/systemd/system/<service-name>.service
sudo vi /etc/systemd/system/vscode-server.service
```

Sample file

```bash
# Description=<service description>
Description=VSCode Server

Wants=network.target
After=syslog.target network-online.target

[Service]
Type=simple
Environment="PASSWORD=<password>"
ExecStart=/home/chris/tools/code-server/code-server
Restart=on-failure
RestartSec=10
KillMode=process
User=<user>

[Install]
WantedBy=multi-user.target
```

Service lifecycle

```bash
# Reload services
sudo systemctl daemon-reload
# Enable the new service
sudo systemctl enable vscode-server.service
# Start the new service
sudo systemctl start vscode-server.service
# Check on the service
systemctl status vscode-server.service
```

## Starting a service on startup

Follow instructions from [the tensorflow site](https://www.tensorflow.org/install/source).   We need to have the right version of bazel installed, which can be sourced from their release site.  Like other downloadable .sh files we'll need to manually clear the quarantine bit.

```bash
sudo xattr -c <path to bazel installation file>
```

When it comes to the bazel build we need to remind it about the capabilities of our host machine to avoid some of the runtime warnings and take advantage of any speedups we can.

```bash
bazel build -c opt --copt=-mavx --copt=-mavx2 --copt=-mfma --copt=-msse4.2 -k //tensorflow/tools/pip_package:build_pip_package
```