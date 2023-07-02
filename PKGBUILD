# Maintainer: Chaiwat Suttipongsakul <cwt@bashell.com>

pkgbase=linux-cwt-rt-515-starfive-visionfive2
_variant=cwt-rt #5.15-VF2-xxx-x
pkgver=3.1.5
epoch=14 #Based on cwt image version
pkgrel=1
_tag=VF2_v${pkgver}
_desc='Linux 5.15.x (-cwt) for StarFive RISC-V VisionFive 2 Board'
_srcname=linux-$_tag
_3rdpart=soft_3rdpart-$_tag
url="https://github.com/starfive-tech/linux/"
arch=(riscv64)
license=('GPL2')
makedepends=(bc libelf pahole cpio perl tar xz clang lld)
options=('!strip')
source=("https://github.com/starfive-tech/linux/archive/refs/tags/${_tag}.tar.gz"
  'linux-0-5.15.0-5.15.2.patch'
  'linux-1-Revert-fbcon-Disable_accelerated_scrolling.patch'
  'linux-2-fbcon-Add_option_to_enable_legacy_hardware_acceleration.patch'
  'linux-3-riscv-zba_zbb.patch'
  'linux-4-eswin_6600u-llvm.patch'
  'linux-5-fix_CVE-2022-0847_DirtyPipe.patch'
  'linux-6-fix_wrong_offset_for_fwcfg_and_make_whole_QSPI_be_available.patch'
  'linux-7-constify_struct_dh_pointer_members.patch'
  'linux-8-fix_broken_gpu-drm-i2c-tda998x.patch'
  'linux-9-patch-5.15-rt17.patch'
  'linux-10-risc-v-rt.patch'
  'linux-11-lazy-preempt.patch'
  'config'
  'linux.preset'
  '90-linux.hook'
  "${_3rdpart}.tar.gz::https://github.com/starfive-tech/soft_3rdpart/archive/refs/tags/${_tag}.tar.gz"
  'soft_3rdpart-0-correct_kernel_source_dir.patch'
  'soft_3rdpart-1-use_clang_for_llvm.patch'
  'soft_3rdpart-mkinitcpio.conf'
  '91-soft_3rdpart.hook')

sha256sums=('68cc09da7fb91010df7048b6b970ea1a3883cef1f5f2022786dbdb8c1a3b52d5'
            '3bd9dc1b0843b77b51b269ad2ca30895121d94a6993f149496a7c9a83e08b369'
            '1582369c7a9365d98a03e08d0dbe8e0affc9417672f00aa57d6957ba559da878'
            'e16e2f8eafe310a561a553d8e2af16af7a50d2c499221d0b9348a94aea571dfa'
            'ef2196f0626265198454972dce9e873b620382465b4e66380de6506ccfc564d0'
            'bcd1f14392af6adce2760c36a7d1b631c60a4f590bf1241934c401187ba1b40e'
            '725875c1d8c7bf93cadfbceedcdfaa4062661b2deeb70a75852b87cff1d50831'
            '95d40b97e61245095fa4dcfd67669d71d9a72c346a8ae22003352090e39ae1e1'
            '01cf756c307a4aeda0b8c940340b75759f00ec712b9ccc217889c6ea8f94f59e'
            'a5955ef6043e89080be902f9133f56fbeb78919fa7b45d4decb9191875217897'
            'fe95794ce3ea00173e8017756aebed7e676ebf80dcc32474df8eb7dd9502f6c7'
            '1d73bda47e9b000e4ab3ee8bcf5e06c6b453bf4c8f5f7643929088f99cdb68dd'
            'e658f8d88c97d1c57adfaf0c99c00923fd1edc78554c6e9744146386c889fecf'
            '8849c840774a4edbd8e433750d20b1a19831c60c948551542c74ae5d736da407'
            '57acae869144508c5600d6c8f41664f073f731c40cad2c58d2a1d55240495ddb'
            'a8f7b233de2ead3177d2623e7f2221b64f8fe872524a90cf82a791d2f508b0fe'
            'a6306ae39d08b6f6385586ee4099d7731c022dd37f938885ba8538f65f87c548'
            '2492020565e8e6157876c2bee48af32dd3fc7967bd418fe6d2d9d9ea0bb72bf1'
            '800e2ca5970c1869282f99f19994c7ad2cbb05a6f3e059d692e30746f2c9b577'
            '5f1c56261d308e968a8dd161e4d5db25b378b73313749e0ca23eb2ef32af9dad'
            '15162c5405104f9d8b27cbb83ea218c52ed939e9a79b71b156e1f320e7c21ad7')

prepare() {
  cd $_srcname

  local src
  for src in $(ls ../linux-*.patch); do
    echo "Applying patch $src..."
    patch -Np1 -F3 <"../$src"
  done

  echo "Setting version..."
  scripts/setlocalversion --save-scmversion
  echo "-${_variant}" >localversion.10-variant
  echo "-${pkgver}" >localversion.20-pkgver
  echo "-$pkgrel" >localversion.30-pkgrel

  echo "Setting config..."
  cp ../config .config
  make LLVM=1 CC="clang -mcpu=sifive-u74 -mtune=sifive-7-series" olddefconfig
  cp .config ../../config.new

  make LLVM=1 CC="clang -mcpu=sifive-u74 -mtune=sifive-7-series" -s kernelrelease >version
  echo "Prepared $pkgbase version $(<version)"

  cd $srcdir/$_3rdpart

  local src
  for src in $(ls ../soft_3rdpart-*.patch); do
    echo "Applying patch $src..."
    patch -Np1 <"../$src"
  done
}

build() {
  cd $_srcname
  make LLVM=1 CC="clang -mcpu=sifive-u74 -mtune=sifive-7-series" all

  # JPU
  cd $srcdir/$_3rdpart/codaj12/jdi/linux/driver
  make LLVM=1 CC="clang -mcpu=sifive-u74 -mtune=sifive-7-series" KERNELDIR=$srcdir/$_srcname

  # VENC
  cd $srcdir/$_3rdpart/wave420l/code/vdi/linux/driver
  make LLVM=1 CC="clang -mcpu=sifive-u74 -mtune=sifive-7-series" KERNELDIR=$srcdir/$_srcname

  # VDEC
  cd $srcdir/$_3rdpart/wave511/code/vdi/linux/driver
  make LLVM=1 CC="clang -mcpu=sifive-u74 -mtune=sifive-7-series" KERNELDIR=$srcdir/$_srcname
}

_package() {
  pkgdesc="The $_desc kernel and modules"
  depends=(coreutils kmod mkinitcpio)
  optdepends=('wireless-regdb: to set the correct wireless channels of your country'
    'linux-firmware: firmware images needed for some devices')
  provides=("linux=${pkgver}" "WIREGUARD-MODULE")
  conflicts=('linux')

  cd $_srcname
  local kernver="$(<version)"
  local modulesdir="$pkgdir/usr/lib/modules/$kernver"

  echo "Installing boot image..."
  install -Dm644 "arch/riscv/boot/Image.gz" "$modulesdir/vmlinuz"
  install -Dm644 "arch/riscv/boot/Image.gz" "$pkgdir/boot/vmlinuz"

  echo "Installing modules..."
  make INSTALL_MOD_PATH="$pkgdir/usr" INSTALL_MOD_STRIP=1 modules_install

  echo "Installing dtbs..."
  make INSTALL_DTBS_PATH="$pkgdir/usr/share/dtbs/$kernver" dtbs_install
  make INSTALL_DTBS_PATH="$pkgdir/boot/dtbs/" dtbs_install

  # remove build links
  rm "$modulesdir"/build

  install -Dm644 ../linux.preset "${pkgdir}/etc/mkinitcpio.d/linux.preset"
  install -Dm644 ../90-linux.hook "${pkgdir}/usr/share/libalpm/hooks/90-linux.hook"
}

_package-soft_3rdpart() {
  pkgdesc="The soft third part modules for the $_desc kernel"
  license=('proprietary')

  echo "Installing Soft 3rd Part..."

  cd $_srcname
  local kernver="$(<version)"
  local modulesdir="$pkgdir/usr/lib/modules/$kernver"
  local _mod_extra="$modulesdir/extra"

  #JPU
  cd $srcdir/$_3rdpart/codaj12/jdi/linux/driver
  install -Dm644 jpu.ko "$_mod_extra/jpu.ko"
  $srcdir/$_srcname/scripts/sign-file sha1 \
    $srcdir/$_srcname/certs/signing_key.pem \
    $srcdir/$_srcname/certs/signing_key.x509 \
    $_mod_extra/jpu.ko
  xz --lzma2=dict=2MiB -f $_mod_extra/jpu.ko

  # VENC
  cd $srcdir/$_3rdpart/wave420l/code/vdi/linux/driver
  install -Dm644 venc.ko "$_mod_extra/venc.ko"
  $srcdir/$_srcname/scripts/sign-file sha1 \
    $srcdir/$_srcname/certs/signing_key.pem \
    $srcdir/$_srcname/certs/signing_key.x509 \
    $_mod_extra/venc.ko
  xz --lzma2=dict=2MiB -f $_mod_extra/venc.ko

  # VDEC
  cd $srcdir/$_3rdpart/wave511/code/vdi/linux/driver
  install -Dm644 vdec.ko "$_mod_extra/vdec.ko"
  $srcdir/$_srcname/scripts/sign-file sha1 \
    $srcdir/$_srcname/certs/signing_key.pem \
    $srcdir/$_srcname/certs/signing_key.x509 \
    $_mod_extra/vdec.ko
  xz --lzma2=dict=2MiB -f $_mod_extra/vdec.ko

  install -Dm644 $srcdir/$_3rdpart/codaj12/LICENSE.txt "${pkgdir}/usr/share/licenses/${pkgname}/LICENSE"
  install -Dm644 $srcdir/soft_3rdpart-mkinitcpio.conf "${pkgdir}/etc/mkinitcpio.conf.d/${pkgname}.conf"
  install -Dm644 $srcdir/91-soft_3rdpart.hook "${pkgdir}/usr/share/libalpm/hooks/91-soft_3rdpart.hook"
}

_package-headers() {
  pkgdesc="Headers and scripts for building modules for the $_desc kernel"
  depends=(pahole)
  provides=("linux-headers=${pkgver}")
  conflicts=('linux-headers')

  cd $_srcname
  local builddir="$pkgdir/usr/lib/modules/$(<version)/build"

  echo "Installing build files..."
  install -Dt "$builddir" -m644 .config Makefile Module.symvers System.map version
  install -Dt "$builddir/kernel" -m644 kernel/Makefile
  install -Dt "$builddir/arch/riscv" -m644 arch/riscv/Makefile
  cp -t "$builddir" -a scripts

  # required when DEBUG_INFO_BTF_MODULES is enabled
  cp --parents -r -t "$builddir/" tools/bpf/resolve_btfids

  echo "Installing headers..."
  cp -t "$builddir" -a include
  cp -t "$builddir/arch/riscv" -a arch/riscv/include
  install -Dt "$builddir/arch/riscv/kernel" -m644 arch/riscv/kernel/asm-offsets.s

  install -Dt "$builddir/drivers/md" -m644 drivers/md/*.h
  install -Dt "$builddir/net/mac80211" -m644 net/mac80211/*.h

  # https://bugs.archlinux.org/task/13146
  install -Dt "$builddir/drivers/media/i2c" -m644 drivers/media/i2c/msp3400-driver.h

  # https://bugs.archlinux.org/task/20402
  install -Dt "$builddir/drivers/media/usb/dvb-usb" -m644 drivers/media/usb/dvb-usb/*.h
  install -Dt "$builddir/drivers/media/dvb-frontends" -m644 drivers/media/dvb-frontends/*.h
  install -Dt "$builddir/drivers/media/tuners" -m644 drivers/media/tuners/*.h

  # https://bugs.archlinux.org/task/71392
  install -Dt "$builddir/drivers/iio/common/hid-sensors" -m644 drivers/iio/common/hid-sensors/*.h

  echo "Installing KConfig files..."
  find . -name 'Kconfig*' -exec install -Dm644 {} "$builddir/{}" \;

  echo "Removing unneeded architectures..."
  local arch
  for arch in "$builddir"/arch/*/; do
    [[ $arch = */riscv/ ]] && continue
    echo "Removing $(basename "$arch")"
    rm -r "$arch"
  done

  echo "Removing documentation..."
  rm -r "$builddir/Documentation"

  echo "Removing broken symlinks..."
  find -L "$builddir" -type l -printf 'Removing %P\n' -delete

  echo "Removing loose objects..."
  find "$builddir" -type f -name '*.o' -printf 'Removing %P\n' -delete

  echo "Stripping build tools..."
  local file
  while read -rd '' file; do
    case "$(file -bi "$file")" in
    application/x-sharedlib\;*) # Libraries (.so)
      strip -v $STRIP_SHARED "$file" ;;
    application/x-archive\;*) # Libraries (.a)
      strip -v $STRIP_STATIC "$file" ;;
    application/x-executable\;*) # Binaries
      strip -v $STRIP_BINARIES "$file" ;;
    application/x-pie-executable\;*) # Relocatable binaries
      strip -v $STRIP_SHARED "$file" ;;
    esac
  done < <(find "$builddir" -type f -perm -u+x ! -name vmlinux -print0)

  echo "Adding symlink..."
  mkdir -p "$pkgdir/usr/src"
  ln -sr "$builddir" "$pkgdir/usr/src/$pkgbase"
}

pkgname=("$pkgbase" "$pkgbase-soft_3rdpart" "$pkgbase-headers")
for _p in "${pkgname[@]}"; do
  eval "package_$_p() {
    $(declare -f "_package${_p#$pkgbase}")
    _package${_p#$pkgbase}
  }"
done

# vim:set ts=8 sts=2 sw=2 et:
