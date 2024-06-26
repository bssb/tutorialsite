+++
title = 'Identifying unused dependencies with pacgraph'
date = 2024-04-24T06:35:38-04:00
tags = ['arch']
cover = 'pacgraph.svg'
coverCaption = "My system's dependencies at the time of writing this"
draft = false
+++

Pacgraph provides a graph of your Arch Linux system's package dependencies. You can install it from the AUR:
{{< highlight bash >}}
paru -S pacgraph
{{< / highlight >}}

It's helped identify packages that I had no idea were installed. After identifying the ones you don't need, you can remove them:

{{< highlight bash >}}
sudo pacman -R packages_to_remove
{{< / highlight >}}

After you've removed a few packages, you can run use this one-liner to remove any orphaned packages. An orphan is a package that was installed as a dependency of something that you already removed.

{{< highlight bash >}}
sudo pacman -Rns $(pacman -Qtdq)
{{< / highlight >}}

Or my script remove_orphans.sh which includes a bit of error handling:

{{< highlight bash >}}
#!/bin/bash
orphans=$(pacman -Qtdq)
if [ -z "$orphans" ]
then
    echo "No orphans to remove"
else
    sudo pacman -Rns $orphans
fi
{{< / highlight >}}

And then you can run pacgraph a second time after removing some packages, it will show other packages that it didn't previously have room for. If you've ever made the mistake like I have of installing a package group such as kde-applications, then you'll know that it installs a whole lot of junk that you don't want. You might have to run pacgraph a few times before you can locate everything that is unneeded.


I use this bash alias to quickly run it:
{{< highlight bash >}}
alias pg='pacgraph -f /tmp/pacgraph && feh /tmp/pacgraph.svg'
{{< / highlight >}}

That can go in your ~/.bashrc or wherever else your aliases are stored.

## Resources
- [pacgraph github](https://github.com/keenerd/pacgraph) (hasn't been updated in a *long* time)
- [pacgraph AUR package](https://aur.archlinux.org/packages/pacgraph)