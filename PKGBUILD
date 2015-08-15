# Submitter:   Wessel Dirksen "p-we" <wdirksen at gmail dot com>
# Contributor: Tycho LÃ¼rsen "bas-t" (responsible for hosting and development of FFdecsawrapper and more)
# Contributor: Petr Vacek "vaca" (contributing code from sascng-linux3-patch and cardslot.conf for serial port SC readers)
# Contributor: J.P. van Best (implementing new procfs API for kernels >= 3.10 in FFdecsawrapper kernel module)
# Contributor: Oliver Endress (providing improved mutex patch for dvbdev.c)

## !! This package can only be used with ffdecsawrapper-git pre-installed and working

pkgname=ffdecsawrapper-module-standalone
pkgver=3.xx
pkgrel=2
pkgdesc="FFdecsa empowered softcam - Standalone loopback module only"
url=https://github.com/bas-t/ffdecsawrapper.git
arch=('i686' 'x86_64')
license=('GPLv3')
depends=('ffdecsawrapper-git')
makedepends=('git' 'linux-headers')
conflicts=('ffdecsawrapper-module-standalone')
provides=('ffdecsawrapper-module-standalone')
install='ffdecsawrapper-module-standalone.install'

_basekernel=`uname -r | cut -c 1-4`

	if [ "$_basekernel" -lt "3.13" ]; then
		_patch="kernel-mutex2.patch"
	else
		_patch="kernel-mutex-new2.patch"
	fi

# !! You must use the same ffdecsawrapper branch in the line below as with ffdecsawrapper-git:

source=('git://github.com/bas-t/ffdecsawrapper.git#branch=stable'
	"http://www.kernel.org/pub/linux/kernel/v3.x/linux-$_basekernel.tar.xz" \
	'ffdecsawrapper-module-standalone.install' 'kernel-mutex2.patch' 'kernel-mutex-new2.patch')

sha256sums=('SKIP'
            'SKIP'
            '16c36bf8bdbe4281c950c61bf5ac49b90b766f1341becd0ac4b5dc547e2b18d6'
            '6e73d5b2280fecb5d19f5f45d6d746f174e99505270ea5b5bc9fcc1a8428d0ae'
            '515bb3888bb675257f5b4305d3725ef2c6977520ad5ae649199895b4422b6034')


pkgver() {

	cd $srcdir/ffdecsawrapper
 	_gitver=`git describe --always | sed 's|-|.|g'`
	_kernel=`uname -r | sed -r 's/-/_/g'`
	echo "$_gitver"_"$_kernel"
}

prepare() {

	msg "Applying kernel-mutex patch..."
	cd $srcdir/linux-$_basekernel
	patch -p1 < $srcdir/$_patch
}

build() {
 
    cd $srcdir/linux-$_basekernel/drivers/media/dvb-core
    make -C /usr/lib/modules/`uname -r`/build M=$(pwd) modules

    	if ! [ "$_basekernel" -lt "3.13" ]; then
    		cp -f $srcdir/linux-$_basekernel/drivers/media/dvb-core/Module.symvers $srcdir/ffdecsawrapper/dvbloopback/module/Module.symvers
    	fi

    cd $srcdir/ffdecsawrapper
    ./configure --update=no
}

package() {

	mkdir -p $pkgdir/usr/lib/modules/`uname -r`/updates/ffdecsawrapper
	install -m0755 $srcdir/ffdecsawrapper/dvbloopback.ko  $pkgdir/usr/lib/modules/`uname -r`/updates/ffdecsawrapper
	install -m0755 $srcdir/linux-$_basekernel/drivers/media/dvb-core/dvb-core.ko  $pkgdir/usr/lib/modules/`uname -r`/updates/ffdecsawrapper
	find "$pkgdir" -name '*.ko' -exec gzip -9 {} \;
}

