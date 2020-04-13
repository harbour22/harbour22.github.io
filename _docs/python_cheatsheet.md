---
title: "Python Cheatsheet"
permalink: /docs/python_cheatsheet/
excerpt: "Useful hints and reminders for python"
toc: false
classes: wide
---

## Setting up jupyterlab notebook server

```bash
# mitigates the "Python.h: No such file or directory" error
sudo apt-get install python3-dev
pip install wheel
pip install jupyterlab
```

Create the required config

```bash
jupyter lab --generate-config
Writing default config to: /home/<username>/.jupyter/jupyter_notebook_config.py
```
