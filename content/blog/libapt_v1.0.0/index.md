+++
title = "Libapt v1.0.0"
date = 2024-11-01
description = "A first version if libapt is ready to use!"
[taxonomies]
tags = ["Rust", "library", "APT", "Debian"]

[extra]
quick_navigation_buttons = true
+++

A first version of _libapt_ is ready to use and published on [crates.io](https://crates.io/crates/libapt).

You can find the sources at [Codeberg](https://codeberg.org/tomirgang/libapt) and [Github](https://github.com/lts-linux/libapt).

## Architecture

_Libapt_ provides a (almost) pure Rust interface for Debian repositories.

On my Ubuntu subsystem I had to install the following dependencies:
    - rust-lzma requires _liblzma-dev_ and _pkg-config_
    - reqwest requires _libssl-dev_


The goal of _libapt_ is to provide a pure Rust library for interfacing with Debian apt repositories.
It shall be possible to use _libapt_ to parse an apt repository,
get the packages, package metadata, checksums and locations,
and verify the validity of the metadata with respect to
signatures, compliance with the Debian repository format specification,
and existence of packages indices and packages.

#### Struct Distro

The struct [Distro] is the starting point to parse an apt repository.
It groups all information which need to be provided by a user to locate
and verify the _InRelease_ distribution index.

For verifying the integrity of the distribution index, a public key
needs to be provided. This pubic key can be armored public GPG key,
e.g. form a URL or a local file, or a non-armored binary GPG key,
e.g. form the local `/etc/apt/trusted.gpg.d/`.
For receiving both types of keys, a download is tried if the provided string starts with 'http',
else the string is interpreted as a local path.

If no verification is wanted, the value _Key::NoSignatureCheck_ can be provided.

```rust
use libapt::{Distro, Key};

let key = Key::key("/etc/apt/trusted.gpg.d/ubuntu-keyring-2018-archive.gpg");

// Default repository format using a public key form an URL.
let distro = Distro::repo(
    "http://archive.ubuntu.com/ubuntu",
    "jammy",
    key.clone(),
);

// Flat repository format using a public key form an URL.
let distro = Distro::flat_repo(
    "http://archive.ubuntu.com/ubuntu",
    "dists/jammy",
    key.clone(),
);

// Flat repo skipping verification.
let distro = Distro::flat_repo(
    "http://archive.ubuntu.com/ubuntu",
    "dists/jammy",
    Key::NoSignatureCheck,
);
```

#### Struct Release

The struct [Release] groups all information contained in the InRelease file.
When a new [Release] is created by running [Release::from_distro],
the content of the InRelease file is downloaded,
the inline signature is verified using the [Distro] key,
and the content is parsed.

```rust
use libapt::{Distro, Key, Release};

// Ubuntu Jammy signing key.
let key = Key::key("/etc/apt/trusted.gpg.d/ubuntu-keyring-2018-archive.gpg");

// Ubuntu Jammy distribution.
let distro = Distro::repo(
    "http://archive.ubuntu.com/ubuntu",
    "jammy",
    key,
);

// Parse the InRelease file.
let release = Release::from_distro(&distro).unwrap();

// Check for compliance with https://wiki.debian.org/DebianRepository/Format#A.22Release.22_files.
release.check_compliance();
```

The struct [Release] provides also some convenience methods to access the package indices.

The method [Release::get_package_links] provides all available package indices.
This means, for each combination of architecture and component,
the function [get_etag] is used to check if the URL really exists,
and if the URL exists, the [Link] is provided.
The return value of this method is a _Vec<(String, Architecture, Link)>_,
and each tuple consists of _component name_, _architecture_, and _URL to the index as [Link]_.

The method [Release::get_package_index_link] provides the [Link] to one specific package index.
The parameters are the _component name_ and the _architecture_, and the result is a _[Link]_.

#### Struct Link

The [Link] struct groups references to files, i.e. URLs,
with the hash sums to verify the file, and the size of the file.
The supported hash types are MD5, SHA1, SHA256 and SHA512.
When the file is downloaded, the best available hash is verified to ensure the integrity.

#### Struct PackageIndex

The struct [PackageIndex] groups all packages of one component and architecture.
This structure also provides support for parsing the apt repository package index files.

```rust
use libapt::{Distro, Key, Release, PackageIndex, Architecture};

// Ubuntu Jammy signing key.
let key = Key::key("/etc/apt/trusted.gpg.d/ubuntu-keyring-2018-archive.gpg");

// Ubuntu Jammy distribution.
let distro = Distro::repo(
    "http://archive.ubuntu.com/ubuntu",
    "jammy",
    key,
);

// Parse the InRelease file.
let release = Release::from_distro(&distro).unwrap();

// Parse the package index of the main component for the amd64 architecture.
let main_amd64 = PackageIndex::new(&release, "main", &Architecture::Amd64).unwrap();

println!("Ubuntu Jammy main provides {} packages for amd64.", main_amd64.package_count());

// Get a Package from the package index.
let busybox = main_amd64.get("busybox-static", None).unwrap();

println!("Ubuntu Jammy main provides busybox-static version {:?}.", busybox.version);
```

#### Struct Package

The metadata about binary packages are grouped in the struct [Package].
The packages are parsed from the so called _stanzas_ of the package indices.

### Struct SourceIndex

The struct [SourceIndex] groups all source packages of one component.
This structure also provides support for parsing the apt repository source package index files.

```rust
use libapt::{Distro, Key, Release, SourceIndex};

// Ubuntu Jammy signing key.
let key = Key::key("/etc/apt/trusted.gpg.d/ubuntu-keyring-2018-archive.gpg");

// Ubuntu Jammy distribution.
let distro = Distro::repo(
    "http://archive.ubuntu.com/ubuntu",
    "jammy",
    key,
);

// Parse the InRelease file.
let release = Release::from_distro(&distro).unwrap();

// Parse the package index of the main component for the amd64 architecture.
let main_sources = SourceIndex::new(&release, "main").unwrap();

println!("Ubuntu Jammy main provides {} source packages.", main_sources.package_count());

// Get a Package from the package index.
let busybox = main_sources.get("busybox", None).unwrap();

println!("Ubuntu Jammy main provides busybox version {:?}.", busybox.version);
```

#### Struct Source

The metadata about source packages are grouped in the struct [Source].
The source packages are parsed from the so called _stanzas_ of the source package indices.
