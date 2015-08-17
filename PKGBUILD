# This is an example PKGBUILD file. Use this as a start to creating your own,
# and remove these comments. For more information, see 'man PKGBUILD'.
# NOTE: Please fill out the license field for your package! If it is unknown,
# then please put 'unknown'.

# Maintainer: Bradley Pesicka <bradley.c.pesicka@gmail.com>
pkgname=ufasoft-bitcoin-miner-nogpu
pkgver=0.66
pkgrel=1
epoch=
pkgdesc="CPU bitcoin miner without support for GPU processing"
arch=('i686')
url="http://ufasoft.com/open/bitcoin/"
license=('GPL3')
groups=()
depends=(
  'pcre' 
  'curl'
  'jansson'
)
makedepends=(
  'jwasm'
  'jansson'
)
checkdepends=()
optdepends=()
provides=()
conflicts=()
replaces=()
backup=()
options=()
install=
changelog=
source=("http://ufasoft.com/files/ufasoft_coin-$pkgver.tar.xz")
noextract=()
sha1sums=(
  '474267c853dd89efdb9155b10cb2cba67ad67e30'
)

build() {
  cd "$srcdir/ufasoft_coin-$pkgver"
  sed -e '27i el/crypto/blake.cpp \\' -i Makefile.am
  sed -e '112i el/crypto/blake.cpp \\' -i Makefile.am
  aclocal
  automake
  ./configure --prefix=/usr --disable-opencl --disable-cuda
  make
}

check() {
  cd "$srcdir/ufasoft_coin-$pkgver"
  make -k check
}

package() {
  cd "$srcdir/ufasoft_coin-$pkgver"
  make DESTDIR="$pkgdir/" install
  mkdir -p "$pkgdir/usr/lib/systemd/system/"
  (
    echo '[Unit]'
    echo 'Description=Ufasoft Bitcoin Miner'
    echo
    echo '[Service]'
    echo 'EnvironmentFile=/etc/conf.d/ufasoft-bitcoin-miner.conf'
    echo 'ExecStart=/usr/bin/nice /usr/bin/coin-miner $COIN_ARGS -o $MINE_WORKER_URL -u $MINE_WORKER_USER -p $MINE_WORKER_PASSWORD 2>&1 &'
    echo 'ExecStop=/bin/pkill /usr/bin/coin-miner'
    echo
    echo '[Install]'
    echo 'WantedBy=multi-user.target'
  ) \
  > "$pkgdir/usr/lib/systemd/system/ufasoft-bitcoin-miner.service"

  mkdir -p "$pkgdir/etc/conf.d/"
  (
    echo '# Settings for the bitcoin mining pool'
    echo 'MINE_WORKER_URL='
    echo 'MINE_WORKER_USER='
    echo 'MINE_WORKER_PASSWORD='
    echo 'COIN_ARGS=-a 1 -l no'
  ) > "$pkgdir/etc/conf.d/ufasoft-bitcoin-miner.conf.default"
}

# vim:set ts=2 sw=2 et:
