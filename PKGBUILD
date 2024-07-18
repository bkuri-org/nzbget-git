# Maintainer: bkuri <aur+nzbget-git@bkuri.com>
# Contributor: selfdenial <selfdenial@pm.me>
# Contributor: Nicola Hinssen <nicola.hinssen@gmail.com>
# Contributor: Jan Holthuis <holthuis.jan@googlemail.com>

pkgname=nzbget-git
pkgver=24.2
pkgrel=20240717
pkgdesc="Download from Usenet using .nzb files (testing release)"
arch=('x86_64')
url="https://github.com/nzbgetcom/nzbget"
license=('GPL')
depends=('libxml2' 'openssl')
optdepends=('python: run scripts'
            'unrar: unpacking archives'
            'p7zip: unpacking archives'
            'par2cmdline: verificate and repair PAR 2.0 files')
provides=('nzbget' 'nzbget-systemd')
conflicts=('nzbget' 'nzbget-systemd')
install=nzbget.install
source=("nzbget-${pkgver}-testing-${pkgrel}-amd64.deb::https://github.com/nzbgetcom/nzbget/releases/download/testing/nzbget-${pkgver}-testing-${pkgrel}-amd64.deb"
        "nzbget.service")
sha256sums=('b4bead8d1d90ec96d5bec34a2aaf9552248cdbab73f5a20c2a8c33f5cf83c3d2'
            'e92d2d09e56930475c9f28641a3326a17aa187834e1bd6328a65b6ed7cc25e99')

prepare() {
  mv "nzbget-${pkgver}-testing-${pkgrel}-amd64.deb" "$pkgname-$pkgver.deb"

  ar x "$pkgname-$pkgver.deb"
  tar xf data.tar.xz
}

package() {
  cp -r usr "$pkgdir"

  install -Dm644 nzbget.service "$pkgdir/usr/lib/systemd/system/nzbget.service"

  install -dm 750 "${pkgdir}/var/lib/nzbget"
}
