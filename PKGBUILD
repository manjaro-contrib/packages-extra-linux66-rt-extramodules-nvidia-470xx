# Maintainer: Bernhard Landauer <bernhard@manjaro.org>

# Archlinux credits:
# Maintainer : Thomas Baechler <thomas@archlinux.org>
# Contributor: Alonso Rodriguez <alonsorodi20 (at) gmail (dot) com>
# Contributor: Sven-Hendrik Haase <sh@lutzhaase.com>
# Contributor: Felix Yan <felixonmars@archlinux.org>
# Contributor: loqs
# Contributor: Dede Dindin Qudsy <xtrymind+gmail+com>
# Contributor: Ike Devolder <ike.devolder+gmail+com>

_linuxprefix=linux66-rt
_extramodules=extramodules-6.6-rt-MANJARO

pkgname=$_linuxprefix-nvidia-470xx
pkgdesc="NVIDIA drivers for linux"
pkgver=470.199.02
pkgrel=1
arch=('x86_64')
url="http://www.nvidia.com/"
license=('custom')
groups=("$_linuxprefix-extramodules")
depends=("$_linuxprefix" "nvidia-utils=$pkgver")
makedepends=("$_linuxprefix-headers")
provides=("nvidia=$pkgver" 'NVIDIA-MODULE')
replaces=('linux515-rt-nvidia-470xx' 'linux60-rt-nvidia-470xx')
options=(!strip)
install=nvidia.install
_durl="https://us.download.nvidia.com/XFree86/Linux-x86"
source=("${_durl}_64/${pkgver}/NVIDIA-Linux-x86_64-${pkgver}-no-compat32.run"
        'kernel-6.4.patch'
        'kernel-6.5.patch'
        'kernel-6.6.patch')
sha256sums=('9c86f9ef6aceaf2b292407aa161b98d817b2eb10a615f971d29a20c2a748ad09'
            '9fbab269f00beb78b44e4693ea44b399e4122a3dfba00322af3e5e3485a1eed3'
            '8688f9d70b34e8111722c0231f773050a62a5d1c1b56c88aa3bab8ebf502902a'
            '0297a7faac22075bb6d93e137d1dc6d1f11c87da855b273432cd4451cde8cfbe')

_pkg="NVIDIA-Linux-x86_64-${pkgver}-no-compat32"

prepare() {
    sh "${_pkg}.run" --extract-only

    cd "${_pkg}/kernel"
    patch -Np1 -i ${srcdir}/kernel-6.4.patch
    patch -Np1 -i ${srcdir}/kernel-6.5.patch
    patch -Np1 -i ${srcdir}/kernel-6.6.patch
}

build() {
    _kernver="$(cat /usr/lib/modules/${_extramodules}/version)"

    cd "${_pkg}"
    export DISTCC_DISABLE=1
    export CCACHE_DISABLE=1
    export IGNORE_PREEMPT_RT_PRESENCE=1
    make -C kernel SYSSRC=/usr/lib/modules/"${_kernver}/build" module
}

package() {
    cd "${_pkg}"
    install -Dm644 kernel/*.ko -t "${pkgdir}/usr/lib/modules/${_extramodules}/"

    # compress each module individually
    find "${pkgdir}" -name '*.ko' -exec xz -T1 {} +

    install -Dm644 LICENSE -t "${pkgdir}/usr/share/licenses/${pkgname}/"
}
