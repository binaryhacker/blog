+++
date = "2017-06-14T08:17:12+05:30"
title = "android boot.img"

+++

Android boog.img is a image format used by android to boot the kernel and a internal ram filesystem (rootfs) . This image have the following structure

```c
struct boot_img_hdr {
	uint8_t magic[BOOT_MAGIC_SIZE];
	uint32_t kernel_size;
	uint32_t kernel_addr;
	uint32_t ramdisk_size;
	uint32_t ramdisk_addr;
	uint32_t second_size;
	uint32_t second_addr;
	uint32_t tags_addr;
	uint32_t page_size;
	uint32_t unused[2];
	uint8_t name[BOOT_NAME_SIZE];
	uint8_t cmdline[BOOT_ARGS_SIZE];
	uint32_t id[8];
	uint8_t extra_cmdline[BOOT_EXTRA_ARGS_SIZE];
} __attribute__((packed));
```

This `C structure` defines the structure of the android bootimage format . As you can see there are 3 diffrent sections in this header 

1. Magic
2. Kernel
	- Kernel size
	- Kernel address
3. Ramdisk
	- Ramdisk size
	- Ramdisk address
4. Second
	- Second size
	- Second Address
5. Tags
	- Tags address

