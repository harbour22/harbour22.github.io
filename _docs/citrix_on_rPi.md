---
title: "rPi Citrix setup"
permalink: /docs/citrix_on_rpi/
excerpt: "Details on setting up Citrix Workstation on the rPi"
toc: false
classes: wide
---
Install the "Citrix Workspace app for Linux (ARM HF)" as downloaded from [the citrix site](https://www.citrix.com/en-gb/downloads/workspace-app/linux/workspace-app-for-linux-latest.html).  On debian stretch this should install out of the box with no additional changes.

Once installed we need to expose the existing certificates to Citrix by running

```bash
sudo ln -s /usr/share/ca-certificates/mozilla/* /opt/Citrix/ICAClient/keystore/cacerts
```

This should resolve any cert issues seen when launching from a downloaded .ica file.