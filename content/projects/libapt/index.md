+++
title = "Libapt"
date = 2024-10-30
updated = 2024-11-02
description = "Libapt is a pure Rust apt library."
weight = 100

[extra]
local_image = "/projects/libapt/Libapt.webp"

[taxonomies]
tags = ["Rust", "library", "APT", "Debian"]
+++

<img src="/projects/libapt/Libapt.webp" width="50%" />

Libapt is a pure Rust apt library.

You can find the sources at [Codeberg](https://codeberg.org/tomirgang/libapt) and [Github](https://github.com/lts-linux/libapt).

## Architecture

This crate provides a pure Rust interface for Debian repositories.

### Debian apt repositories

The format of Debian, i.e. apt, repositories is described in the [Debian wiki](https://wiki.debian.org/DebianRepository/Format#Debian_Repository_Format).

In general, apt repositories consist of distributions and packages.
The path to this file on the server depends on the repository type.
By default, a apt repository is structured in the following way:

```plain
Root --- /dists ------ /dist_a
      \          \---- /dist_b
       \          \--- ...
        \
         \--/pool ----- /a
                   \--- /b                  
                    \-- ...
```

The dists folder contains one or many distributions.

Each distribution is defined by a _InRelease_, or _Release_, index file.
This index file is signed using GPG key of the distribution provider.
The matching public key is typically installed on the local machine,
and the client tool verifies the signature to ensure the repository was not manipulated.

The distribution index file contains relative paths and checksums to package index files.
These package indices are grouped by _components_.
The client tool downloads the wanted component indices,
verifies the integrity using the checksum,
and parses the packages _[stanzas](https://wiki.debian.org/DebianRepository/Format#A.22Packages.22_Indices)_.

The package indices provide, as part of the stanzas, paths to the packages,
relative to the root of the apt repository, and checksums for each package.
These checksums allow verification of the packages after download.

The packages are typically placed in a _pool_ folder,
and this folder typically uses a sub-folder structure consisting of
first letter or lib plus first letter and name of the source package.
This structure is not relevant for the apt repository client tooling,
because it's part of the paths provided in the component package indices.

Some tools generate _[flat repositories](https://wiki.debian.org/DebianRepository/Format#Flat_Repository_Format)_ instead of default repositories. 
These repositories work in the same way, but use a _path_ instead of a distribution name.
The distribution _InRelease_ file for these repositories is located at _root_ / _path_ / _InRelease_.
These repositories typically also doesn't follow the pool structure for organizing the packages.

### libapt

The goal of _libapt_ is to provide a pure Rust library for interfacing with Debian apt repositories.
It shall be possible to use _libapt_ to parse an apt repository,
get the packages, package metadata, checksums and locations,
and verify the validity of the metadata with respect to
signatures, compliance with the Debian repository format specification,
and existence of packages indices and packages.

[Read more](https://github.com/lts-linux/libapt)