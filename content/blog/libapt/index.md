+++
title = "Libapt"
date = 2024-10-30
description = "Libapt is a pure Rust apt library."
[taxonomies]
tags = ["Rust", "library", "APT", "Debian"]

[extra]
quick_navigation_buttons = true
+++

## New project started!

I started a new project called _libapt_.
The goal of _libapt_ is to provide a pure Rust library for interfacing with Debian apt repositories.
It shall be possible to use _libapt_ to parse an apt repository,
get the packages, package metadata, checksums and locations,
and verify the validity of the metadata with respect to
signatures, compliance with the Debian repository format specification,
and existence of packages indices and packages.

## Why is is needed?

Debian is a great base for building custom embedded Linux distributions,
because it supports a huge amount of architectures,
and provides very long support for the packages.
This reduces the burden of maintaining an embedded Linux solution a lot.

Unfortunately, the available _apt_ tools are not built for my use case.
I want to track in detail what changes over time and I want to do
this for _apt_ repositories not configured on my host machine.
_Libapt_ shall build the base of a small tool set supporting building of
embedded Linux root file systems form _apt_ repositories.
