# Maintainer: bkuri <aur+nzbget-git@bkuri.com>
# Contributor: selfdenial <selfdenial@pm.me>
# Contributor: Nicola Hinssen <nicola.hinssen@gmail.com>
# Contributor: Jan Holthuis <holthuis.jan@googlemail.com>

pkgbase=nzbget-git
pkgname=(nzbget-git nzbget-git-debug)
pkgver=24.6.r2542.f3d8ce63
pkgrel=1
pkgdesc="Download from Usenet using .nzb files (testing release)"

arch=('x86_64')
install=nzbget.install
license=('GPL')
options=('debug' '!lto')
url="https://github.com/nzbgetcom/nzbget"

depends=('libxml2' 'openssl')
makedepends=('cmake' 'boost' 'git')
optdepends=('nzbget-git-debug: Debug symbols for nzbget'
			'python: run scripts'
            'unrar: unpacking archives'
            'p7zip: unpacking archives')

conflicts=('nzbget' 'nzbget-systemd')
provides=('nzbget' 'nzbget-systemd')

source=("$pkgname::git+https://github.com/nzbgetcom/nzbget.git#branch=develop"
        "nzbget.service")

sha256sums=('SKIP'
            'e92d2d09e56930475c9f28641a3326a17aa187834e1bd6328a65b6ed7cc25e99')

prepare() {
  cd "${srcdir}/${pkgbase}"
  
  # Ensure build directory exists
  mkdir -p build
  
  # Set environment variables to control build locations
  export HOME="${srcdir}"
  export XDG_CACHE_HOME="${srcdir}/.cache"
  export XDG_DATA_HOME="${srcdir}/data"
  export XDG_CONFIG_HOME="${srcdir}/config"
}

build() {
  cd "${srcdir}/${pkgbase}/build"

  # Configure the build to generate debug symbols
  cmake .. \
    -DCMAKE_INSTALL_PREFIX=/usr \
    -DCMAKE_BUILD_TYPE=RelWithDebInfo \
    -DCMAKE_POLICY_DEFAULT_CMP0167=NEW \
    -DDEBUG_SYMBOLS=ON

  # Build the project
  cmake --build . -j $(nproc)
}

check() {
  cd "${srcdir}/${pkgbase}/build"
  ctest --output-on-failure
}

package_nzbget-git() {
  cd "${srcdir}/${pkgname}/build"
  DESTDIR="$pkgdir" cmake --install .
  
  # Install systemd service file
  install -Dm644 "${srcdir}/nzbget.service" "${pkgdir}/usr/lib/systemd/system/nzbget.service"
  
  # Create necessary directories
  install -dm 755 "${pkgdir}/var/lib/nzbget"
  install -dm 755 "${pkgdir}/usr/share/nzbget"
  
  # Install additional files
  cd "${srcdir}/${pkgname}"
  install -Dm 644 README.md "${pkgdir}/usr/share/nzbget/README.md"
  install -Dm 644 nzbget.conf "${pkgdir}/usr/share/nzbget/nzbget.conf"

  # Remove unnecessary build artifacts
  rm -rf "${pkgdir}/usr/src"
  rm -rf "${pkgdir}/home"
  
  # Strip binary (added back)
  cd "$pkgdir/usr/bin"
  if [[ -f nzbget && "$(file -bi nzbget)" == *"application/x-executable"* ]]; then
    strip --strip-all nzbget
  fi
}

package_nzbget-git-debug() {
  pkgdesc="Debug symbols for nzbget-git"
  depends=("nzbget-git=$pkgver")
  options=('!strip')

  # Create debug directory
  install -dm755 "${pkgdir}/usr/lib/debug"

  # Copy debug symbols
  cd "${srcdir}/${pkgbase}/build"
  find . -name "*.debug" -exec install -Dm644 {} "${pkgdir}/usr/lib/debug/{}" \;

  # Optionally, copy full debug symbols for the binary
  if [[ -f "${srcdir}/${pkgbase}/build/nzbget" ]]; then
    objcopy --only-keep-debug "${srcdir}/${pkgbase}/build/nzbget" "${pkgdir}/usr/lib/debug/usr/bin/nzbget.debug"
  fi
}

pkgver() {
  cd "${srcdir}/${pkgbase}"
  
  # Extract version from CMakeLists.txt
  local _pkgver=$(grep -oP 'set\(VERSION "\K[^"]+' CMakeLists.txt)
  
  # Get commit count and short hash
  local _rev=$(git rev-list --count HEAD)
  local _hash=$(git rev-parse --short HEAD)
  
  # Construct and print version string
  printf "%s.r%s.%s" "${_pkgver}" "${_rev}" "${_hash}"
}
