How to build Linux image with openh264.

## Compare with pc_x86_64_bios_defconfig
Config qemu_x86_64_openh264_defconfig works (because in x86-64 architecture SIMD is build-in).  
Config [qemu_x86_openh264_defconfig](my_external_tree/configs/qemu_x86_openh264_defconfig) is based on [qemu_x86_defconfig](https://github.com/buildroot/buildroot/blob/e82217622ea4778148de82a4b77972940b5e9a9e/configs/qemu_x86_defconfig). You can see log of this config in file x86_non-simd_openh264_meson_fail.log (produced by `make -C buildroot > "x86_non-simd_openh264_meson_fail.log"`); How to build Linux image with openh264 for non-SIMD x86 CPU (like Geode)? I think problem is inside openh264's meson build script presupposes SIMD support, see [topic in cisco repo](https://github.com/cisco/openh264/issues/3545#issuecomment-2453704293) and buildroot [issue](https://gitlab.com/buildroot.org/buildroot/-/issues/64).

## Clone
```
git clone --remote-submodules --recurse-submodules -j8 https://github.com/AndreiCherniaev/buildroot_x86_non-simd_openh264_meson.git
cd buildroot_x86_non-simd_openh264_meson
```
## Add my patch
[Patch](https://buildroot.org/downloads/manual/manual.html#_adding_project_specific_patches_and_hashes) buildroot using [files](https://lore.kernel.org/buildroot/20250309162614.2549737-1-dungeonlords789@naver.com/T/#t).
## Make image
In example I use qemu_x86_64_openh264_defconfig but you also should try qemu_x86_openh264_defconfig
```
make clean -C buildroot
make BR2_EXTERNAL=$PWD/my_external_tree -C $PWD/buildroot qemu_x86_64_openh264_defconfig
make -C buildroot
```
## Save non-default buildroot .config
To save non-default buildroot's buildroot/.config to $PWD/my_external_tree/configs/f2fs_qemu_x86_defconfig
```
make -C $PWD/buildroot savedefconfig BR2_DEFCONFIG=$PWD/my_external_tree/configs/qemu_x86_64_openh264_defconfig
```
## Start in QEMU
This code is based on emulation [script1](https://github.com/buildroot/buildroot/blob/02540771bccf7b10c7daecce5f0e1e41a73c1e07/boot/grub2/readme.txt#L4) and [script2](https://github.com/buildroot/buildroot/blob/9e3d572ff532df945fbc282fed22d10098e5718b/board/pc/readme.txt), for qemu_x86_64_openh264_defconfig run the emulation with:
```
qemu-system-x86_64 -M pc -kernel buildroot/output/images/bzImage -drive file=buildroot/output/images/rootfs.ext2,if=virtio,format=raw -append "rootwait root=/dev/vda console=tty1 console=ttyS0" -serial stdio -net nic,model=virtio -net user
```
For qemu_x86_openh264_defconfig run the emulation with:
```
qemu-system-i386 -M pc -kernel buildroot/output/images/bzImage -drive file=buildroot/output/images/rootfs.ext2,if=virtio,format=raw -append "rootwait root=/dev/vda console=tty1 console=ttyS0" -serial stdio -net nic,model=virtio -net user
```
Note: Optionally add `-nographic` to see output not in extra screen but in console terminal. Or `-display curses` to pseudographic.

## Problems
In case of problems to fix it and then build again
```
make libopenh264-dirclean -C buildroot
make libopenh264-rebuild -C buildroot 2>&1 |tee libopenh264.log
```
Then see log in `libopenh264.log`
