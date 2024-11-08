+++
title = "Libapt v1.3.0"
date = 2024-11-08
description = "Use async downloads."
[taxonomies]
tags = ["Rust", "library", "APT", "Debian"]

[extra]
quick_navigation_buttons = true
+++

With the update to version 1.3.0, _libapt_ makes now use of the async interface for _reqwest_.
This translates into async functions for many interfaces,
but most of the use cases should profit from this change.

You can find the sources at [Codeberg](https://codeberg.org/tomirgang/libapt) and [Github](https://github.com/lts-linux/libapt).

You can find the release at [crates.io](https://crates.io/crates/libapt),
and the documentation is also available on [docs.rs](https://docs.rs/libapt/latest/libapt/).
