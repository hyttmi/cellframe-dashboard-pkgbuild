# Maintainer: Mika Hyttinen <mika dot hyttinen+arch ät gmail dot com>
pkgname="cellframe-dashboard"
_nodename="cellframe-node"
pkgver=2.15.15
pkgrel=1
pkgdesc="Super application for managing Cellframe node"
arch=(x86_64 aarch64)
url="https://cellframe.net"
license=(GPL3)
depends=(qt5-graphicaleffects qt5-base qt5-quickcontrols2 qt5-quickcontrols logrotate libxcrypt-compat)
makedepends=(git qt5-base qt5-declarative cmake python3)
options=(!debug)
source=(git+https://gitlab.demlabs.net/cellframe/$pkgname.git#commit=5555facf496c0d9098ed48182b73f594690f612d
		cellframe-node.logrotate
		cellframe-node-logrotate.timer
		cellframe-node-logrotate.service
		cellframe-node.service)
md5sums=('SKIP'
         '6a52220e0b285dc9e803082f36897ad4'
         '47edb0d55d537e72f3de07ec6a72ea78'
         '7c1087eea7336d99c4af55119673b009'
         '72472d529b38f06a78f37ac659b18d65')
conflicts=(cellframe-node cellframe-wallet cellframe-node-debug)
install=$pkgname.install

prepare() {
	cd "$srcdir/$pkgname"
	sed -i 's|url = ../prod_build_cellframe-dashboard|url = https://gitlab.demlabs.net/cellframe/prod_build_cellframe-dashboard|' .gitmodules
	echo -n "+++ Fetching submodule sources..."
	git submodule sync > /dev/null 2>&1
	git submodule update --init --recursive > /dev/null 2>&1 &
	pid=$!

	while ps -p $pid > /dev/null; do
    	echo -n "."
    	sleep 1
	done

	wait $pid
}

build() {
	cd "$srcdir/$pkgname"
	qmake QMAKE_CFLAGS+="-fpermissive"
	make -j$(nproc)
}

package() {
	cd "$srcdir/$pkgname"
	make INSTALL_ROOT="$pkgdir" install
	install -Dm 644 "$pkgdir/opt/$pkgname/share/CellFrameDashboard.desktop" -t "$pkgdir/usr/share/applications/"
	install -Dm 644 "$pkgdir/opt/$pkgname/share/init.d/$pkgname.service" -t "$pkgdir/usr/lib/systemd/system/"
	install -Dm 644 "$srcdir/$pkgname/LICENSE" -t "$pkgdir/usr/share/licenses/$pkgname"
	install -Dm 644 "$srcdir/$pkgname/$_nodename/LICENSE" -t "$pkgdir/usr/share/licenses/$_nodename"
	install -Dm 644 "$srcdir/$_nodename.logrotate" "$pkgdir/etc/logrotate.d/$_nodename"
	install -Dm 644 "$srcdir/$_nodename-logrotate.service" -t "$pkgdir/usr/lib/systemd/system"
	install -Dm 644 "$srcdir/$_nodename-logrotate.timer" -t "$pkgdir/usr/lib/systemd/system"
	install -Dm 644 "$srcdir/$_nodename.service" -t "$pkgdir/usr/lib/systemd/system"
}
