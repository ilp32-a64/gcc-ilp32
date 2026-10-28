# gcc-ilp32

GCC 14 for the AArch64 ILP32 ABI (`aarch64-linux-gnu_ilp32`), as built for the unofficial
[debian-ilp32](https://github.com/ilp32-a64/debian-ilp32) packages: Debian 13's gcc-14 source with a
few ILP32 fixes on top.

## Branches

| Branch | Content |
| --- | --- |
| `debian-14.2.0-19` | Debian's gcc-14 14.2.0-19 source as `debian/rules unpack` leaves it (first commit), then one commit per Debian patch in the order `debian/rules patch` applies them for a cross build of trixie, then the autoconf run and the generated `gcc/distro-defaults.h` |
| `ilp32-14.2.0-19` | `debian-14.2.0-19` plus the ILP32 fixes below; the source of `gcc-14-aarch64-linux-gnu-ilp32` 14.2.0-19+ilp32a64.2 |

ILP32 fixes (also as patch files in
[test-ilp32](https://github.com/ilp32-a64/test-ilp32) `glibc/toolchain/patches/gcc-14.2.0-debian/`):

1. libgcc: unwind through frames with signed return addresses (PAC)
2. libgcc: emit ELF32 GNU property notes (BTI/PAC objects link with `-z force-bti`)
3. libgomp: keep the configured `omp_nest_lock_t` size (revert Debian's `libgomp-omp_h-multilib`,
   which gives ILP32 a 12-byte nest lock while libgomp uses 24 bytes)
4. libatomic, libitm: emit ELF32 GNU property notes

## Build

The compiler packages are built natively on an arm64 board running Debian 13, in a fresh clean
chroot per build, with Debian's configure options for `aarch64-linux-gnu` adapted to
`--with-abi=ilp32 --with-multilib-list=ilp32 --disable-multilib --disable-multiarch`; see
`build-debian-clean.sh` and `clean-chroot.sh` in test-ilp32 `glibc/toolchain/`. Two such builds gave
byte-identical installs. Test results: libstdc++ and libgomp testsuites on the board, an installed
toolchain test (`toolchain-test.sh`), and debian-ilp32's QEMU tests.

## License

GCC is free software under the GPL version 3 or later, with the GCC Runtime Library Exception for
the runtime libraries; see the COPYING* files in the source branches. The Debian patches are the
work of the Debian GCC maintainers; each of their commits names its `debian/patches` file in the
gcc-14 14.2.0-19 source package.
