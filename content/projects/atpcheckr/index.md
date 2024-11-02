+++
title = "Aptcheckr"
date = 2024-11-02
description = "Command line tool to verify apt repositories."
weight = 90

[extra]
local_image = "/projects/atpcheckr/Aptcheckr.webp"

[taxonomies]
tags = ["Rust", "tool", "APT", "Debian"]
+++

<img src="/projects/atpcheckr/Aptcheckr.webp" width="50%" />

_Aptcheckr_ is a command line tool to verify apt repositories.
It's based in [libapt](https://lts-linux.eu/projects/libapt/)
and verifies that all apt repository metadata is compliant with
the [Debian policy](https://www.debian.org/doc/debian-policy/),
and that the package set is consistent, i.e. all dependencies
and source packages are available.

[Read more](https://github.com/tomirgang/aptcheckr)