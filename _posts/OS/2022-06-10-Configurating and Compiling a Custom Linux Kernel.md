---
title: "「Configurating and Compiling a Custom Linux Kernel」"
subtitle: "Configurating and Compiling a Custom Linux Kernel"
layout: post
author: "XionYono"
header-style: text
hidden: false
tags:
  - OS
---



# Linux Kernel Configuration System —— KConfig

`KConfig` is a platform-independent application settings framework. It is made of two parts: `KConfigCore` and `KConfigGui`.

`KConfigCore` provides access to the configuration files themselves. It features : 

- Code generation
- Cascading Configuration files
- Optional shell expansion support
- The ability to lock down configuration options

`KConfigGui` provides a way to hook widgets to the configuration so that they are automatically initialized from the configuration and automatically propagate their changes to their respective configuration files.

## KConfig file

There are various types of variables on KConfig file. The most useful are :

- `String`: string data
- `bool`: Y/N
- `tristate` : Y/M/N, usually to associate with a detachable component. M implies the related component  will be compiling as a loadable module.

# Linux Kernel Compiling and Installation

## Compiling

### Compiling Tools Installation

```shell
sudo apt-get install build-essential libncurses-dev bison flex libssl-dev libelf-dev
```

### Compiling Linux Kernel and Modules

```shell
sudo make all -j12
```

### Install Modules and Headers

```shell
sudo make modules_install
sudo make headers_install
```

### Install Linux Kernel 

```shell
sudo make install
```

### Configuring grub

```shell
sudo vi /etc/default/grub
sudo update-grub
```

### Reboot to take effect

```shell
sudo reboot
```



## Grub file Introduction

The following are directives commonly used in the Grub configuration file:

- `chainloader <path/to/file>`:  Loads the specified file as a chain loader. Replace `*</path/to/file>*` with the absolute path to the chain loader. If the file is located on the first sector of the specified partition, use the blocklist notation, `+1`
- `default="<integer> > <integer>"`:  for example, 3>1
- `timeout=<integer>`: the time that rests on kernel load options menu

# References

[1] https://api.kde.org/frameworks/kconfig/html/ "KConfig"



