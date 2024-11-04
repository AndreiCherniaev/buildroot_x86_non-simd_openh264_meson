How to build Linux image based on pc_x86_64_bios_defconfig but with libv4l while glibc's BR2_TIME_BITS_64 is enabled.
## Compare with pc_x86_64_bios_defconfig
Config [qemu_x86_openh264_defconfig](my_external_tree/configs/qemu_x86_openh264_defconfig) is based on [qemu_x86_defconfig](https://github.com/buildroot/buildroot/blob/e82217622ea4778148de82a4b77972940b5e9a9e/configs/qemu_x86_defconfig).

## Clone
```
git clone --remote-submodules --recurse-submodules -j8 https://github.com/AndreiCherniaev/buildroot_x86_non-simd_openh264_meson.git
cd buildroot_x86_non-simd_openh264_meson
```
## Make image
```
make clean -C buildroot
make BR2_EXTERNAL=$PWD/my_external_tree -C $PWD/buildroot qemu_x86_openh264_defconfig
make -C buildroot
```
## Save non-default buildroot .config
To save non-default buildroot's buildroot/.config to $PWD/my_external_tree/configs/f2fs_qemu_x86_defconfig
```
make -C $PWD/buildroot savedefconfig BR2_DEFCONFIG=$PWD/my_external_tree/configs/qemu_x86_openh264_defconfig
```
## Start in QEMU
This code is based on emulation [script1](https://github.com/buildroot/buildroot/blob/02540771bccf7b10c7daecce5f0e1e41a73c1e07/boot/grub2/readme.txt#L4) and [script2](https://github.com/buildroot/buildroot/blob/9e3d572ff532df945fbc282fed22d10098e5718b/board/pc/readme.txt), run the emulation with:
```
qemu-system-i386 -M pc -drive file=output/images/disk.img,if=virtio,format=raw -net nic,model=virtio -net user
```
Note: image file `Buildroot.img` is located outside of repo folder so we use `../`. Optionally add `-nographic` to see output not in extra screen but in console terminal. Or `-display curses` to pseudographic.

##Problems
```

```
see `build.log`
