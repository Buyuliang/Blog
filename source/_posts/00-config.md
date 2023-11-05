---
title: 00-config
date: 2023-10-02 12:32:56
tags: [rk3588]
categories: rk3588
---

## 00-config
初始化 config 
``` bash
INIT_CMDS="chip defconfig lunch .*_defconfig olddefconfig savedefconfig menuconfig config default"
init_hook()
{
	case "${1:-default}" in
		chip) shift; choose_chip $@ ;;
		lunch|defconfig) shift; choose_defconfig $@ ;;
		*_defconfig) switch_defconfig "$1" ;;
		olddefconfig | savedefconfig | menuconfig)
			prepare_config
			$MAKE $1
			;;
		config)
			prepare_config
			$MAKE menuconfig
			$MAKE savedefconfig
			;;
		default) prepare_config ;; # End of init
		*) usage ;;
	esac
}
```