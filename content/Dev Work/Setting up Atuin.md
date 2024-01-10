---
title: Setting up Atuin.
---

# Setting up Atuin

[Atuin](https://atuin.sh/) is a really nice tool to improve your terminal history. It uses an sqlite db to store you history, and allows you to nicely query against it.

Also it has a nice look and feel :)

It rebinds ctrl + r, and shows an interface like this:

![[atuin_screenshot.png]]

Lets go over how to install:

Run this:

```bash
bash <(curl https://raw.githubusercontent.com/ellie/atuin/main/install.sh)
```

### installation for bash

We need to setup some hooks, so first install bash-preexec:

```
curl https://raw.githubusercontent.com/rcaloras/bash-preexec/master/bash-preexec.sh -o ~/.bash-preexec.sh
echo '[[ -f ~/.bash-preexec.sh ]] && source ~/.bash-preexec.sh' >> ~/.bashrc
```

Then setup Atuin

```
echo 'eval "$(atuin init bash)"' >> ~/.bashrc
```

—

To not lose all your other history, Atuin has a nice functionality to import you old history, simply use:

```bash
atuin import auto
```

And Atuin will automatically detect the shell you’re using and import your history.

