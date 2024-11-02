+++
title = "Aptcheckr"
date = 2024-11-02
description = "Command line tool to verify apt repositories."
[taxonomies]
tags = ["Rust", "tool", "APT", "Debian"]

[extra]
quick_navigation_buttons = true
+++

## New project started!

I started a new project called _aptcheckr_.
_Aptcheckr_ is a command line tool to verify apt repositories.
It's based in [libapt](https://lts-linux.eu/projects/libapt/)
and verifies that all apt repository metadata is compliant with
the [Debian policy](https://www.debian.org/doc/debian-policy/),
and that the package set is consistent, i.e. all dependencies
and source packages are available.

## Why is is needed?

Debian is a great base for building custom embedded Linux distributions,
because it supports a huge amount of architectures,
and provides very long support for the packages.
This reduces the burden of maintaining an embedded Linux solution a lot.

I build my own derived distribution using [aptly](https://www.aptly.info/),
which works quite good, but I have no way to verify that my selected set
of packages is valid, i.e. all dependencies are available and the packages
fit together according to their metadata. This gap shall be closed using
_aptcheckr_.

In addition, it would be cool to have a tool which can verify if two
apt repositories are compatible. Maybe this can be also closed by
_aptcheckr_.
