# Maintainer: Attila Bogár <attila.bogar@gmail.com>
# Contributor: Tom Alexandrowicz <tom@alexandrowicz.ca>

pkgname=('iguanair' 'python-iguanair' 'python2-iguanair' 'iguanair-reflasher' 'iguanair-lirc')
pkgver=1.2.0
pkgrel=1
pkgdesc="Driver for Iguanaworks USB IR transceiver"
arch=('i686' 'x86_64' 'armv6h' 'armv7h' 'aarch64')
url="https://github.com/iguanaworks/iguanair"
license=('GPL2')
makedepends=('cmake' 'python2' 'python' 'swig' 'lirc')
source=(https://www.iguanaworks.net/downloads/iguanaIR-${pkgver}.tar.gz)

sha256sums=(
  '3352aaccf8c6d55f7c9555e2e88cd7e5af16c433c5763fcc4951c967941708b9'
)

_files_reflasher=(
  'usr/bin/iguanaIR-reflasher'
  'usr/share/iguanaIR-reflasher'
  'usr/share/man/man1/iguanaIR-reflasher.1'
)

build() {
  cd $srcdir/iguanaIR-$pkgver/software/usb_ir/
  patch -p0 < $startdir/iguanair-pr-46.patch
  cmake .
  make iguanaIR

  cd $srcdir/iguanaIR-$pkgver/software/lirc-drv-iguanair
  patch -p0 < $startdir/Makefile.patch
  make IGUANADIR="$srcdir/iguanaIR-$pkgver/software/usb_ir"
}

package_iguanair() {
  install=iguanair.install

  cd $srcdir/iguanaIR-$pkgver/software/usb_ir/
  make DESTDIR=$pkgdir install
  if [ -d "$pkgdir/usr/lib64" ]
  then
    mv -v $pkgdir/usr/lib64/* $pkgdir/usr/lib/
    rm -rf $pkgdir/usr/lib64
  fi
  mkdir -p $pkgdir/usr/lib/systemd/system/
  # remove python bindings + reflasher
  cd $pkgdir
  rm -rvf "${_files_reflasher[@]}" usr/lib/python*
  chown -R 0:0 $pkgdir/
}

package_python-iguanair() {
  pkgdesc="Python 3 module for Iguanaworks USB IR transceiver"
  depends=('iguanair' 'python')
  cd $srcdir/iguanaIR-$pkgver/software/usb_ir/python3
  make DESTDIR=$pkgdir install
  if [ -d "$pkgdir/usr/lib64" ]
  then
    mv -v $pkgdir/usr/lib64 $pkgdir/usr/lib
  fi
  chown -R 0:0 $pkgdir/
}

package_python2-iguanair() {
  pkgdesc="Python 2 module for Iguanaworks USB IR transceiver"
  depends=('iguanair' 'python2')
  cd $srcdir/iguanaIR-$pkgver/software/usb_ir/python2
  make DESTDIR=$pkgdir install
  if [ -d "$pkgdir/usr/lib64" ]
  then
    mv -v $pkgdir/usr/lib64 $pkgdir/usr/lib
  fi
  chown -R 0:0 $pkgdir/
}

package_iguanair-reflasher() {
  pkgdesc="Reflasher for Iguanaworks USB IR transceiver"
  depends=('iguanair' 'python-iguanair')

  cd $srcdir/iguanaIR-$pkgver/software/usb_ir/
  tmpdir="$(mktemp -d)"
  make DESTDIR="$tmpdir" install
  if [ -d "$tmpdir/usr/lib64" ]
  then
    mv -v $tmpdir/usr/lib64/* $tmpdir/usr/lib/
    rm -rf $tmpdir/usr/lib64
  fi
  tar -C "$tmpdir" -cpf - "${_files_reflasher[@]}" \
    | tar -C "$pkgdir" -xpvf -
  rm -rf "$tmpdir"
  chown -R 0:0 $pkgdir/
}

package_iguanair-lirc() {
  pkgdesc="LIRC driver for Iguanaworks USB IR transceiver"
  depends=('iguanair' 'lirc')

  cd $srcdir/iguanaIR-$pkgver/software/lirc-drv-iguanair
  mkdir -p "$pkgdir$(pkg-config --variable=plugindir lirc-driver)"
  mkdir -p "$pkgdir$(pkg-config --variable=configdir lirc-driver)"
  mkdir -p "$pkgdir$(pkg-config --variable=plugindocs lirc-driver)"
  make DESTDIR="$pkgdir" install
  chown -R 0:0 "$pkgdir"
}
