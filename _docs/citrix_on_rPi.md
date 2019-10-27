---
title: "rPi Citrix setup"
permalink: /docs/citrix_on_rpi/
excerpt: "Details on setting up Citrix Workstation on the rPi"
toc: false
classes: wide
---
Install the "Citrix Workspace app for Linux (ARM HF)" downloaded from [workspace app](https://www.citrix.com/en-gb/downloads/workspace-app/linux/workspace-app-for-linux-latest.html).

Once installed we need to expose the existing certificates to Citrix by running

```bash
sudo ln -s /usr/share/ca-certificates/mozilla/* /opt/Citrix/ICAClient/keystore/cacerts
```
