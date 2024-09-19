# Maintainer: bkuri <aur+nzbget-git@bkuri.com>
# Contributor: selfdenial <selfdenial@pm.me>
# Contributor: Nicola Hinssen <nicola.hinssen@gmail.com>
# Contributor: Jan Holthuis <holthuis.jan@googlemail.com>

pkgname=nzbget-git
_pkgver=24.4
pkgver="${_pkgver}.r2510.e5a28375"
pkgrel=1
pkgdesc="Download from Usenet using .nzb files (testing release)"
arch=('x86_64')
url="https://github.com/nzbgetcom/nzbget"
license=('GPL')
depends=('libxml2' 'openssl')
makedepends=('cmake' 'boost' 'git')
optdepends=('python: run scripts'
            'unrar: unpacking archives'
            'p7zip: unpacking archives')
provides=('nzbget' 'nzbget-systemd')
conflicts=('nzbget' 'nzbget-systemd')
install=nzbget.install
source=("$pkgname::git+https://github.com/nzbgetcom/nzbget.git#branch=develop"
	"nzbget.service")
sha256sums=('SKIP'
            'e92d2d09e56930475c9f28641a3326a17aa187834e1bd6328a65b6ed7cc25e99')

pkgver() {
  cd "${srcdir}/${pkgname}"
  printf "%s.r%s.%s" "${_pkgver}" "$(git rev-list --count HEAD)" "$(git rev-parse --short HEAD)"
}

prepare() {
  cd "${srcdir}/${pkgname}"
  mkdir -p build
}

build() {
  cd "${srcdir}/${pkgname}/build"
  cmake .. -DCMAKE_INSTALL_PREFIX="${pkgdir}"/usr
  cmake --build . -j $(nproc)
}

package() {
  cd "${srcdir}/${pkgname}/build"
  cmake --install . --prefix "${pkgdir}"/usr
  cd "${srcdir}"
  install -Dm644 nzbget.service "${pkgdir}/usr/lib/systemd/system/nzbget.service"
  install -dm 750 "${pkgdir}/var/lib/nzbget"
  install -d "${pkgdir}/usr/share/nzbget"
  cd "${srcdir}/${pkgname}"
  install -m 644 -t "${pkgdir}/usr/share/nzbget" README.md
  install -m 644 -t "${pkgdir}/usr/share/nzbget" nzbget.conf
}
