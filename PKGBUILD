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
pkgver=470.239.06
pkgrel=5
arch=('x86_64')
url="http://www.nvidia.com/"
license=('custom')
groups=("$_linuxprefix-extramodules")
depends=("$_linuxprefix" "nvidia-utils=$pkgver")
makedepends=("$_linuxprefix-headers")
provides=("nvidia=$pkgver" 'NVIDIA-MODULE')
replaces=('linux515-rt-nvidia-470xx' 'linux60-rt-nvidia-470xx')
options=(!strip)
_durl="https://us.download.nvidia.com/XFree86/Linux-x86"
source=("${_durl}_64/${pkgver}/NVIDIA-Linux-x86_64-${pkgver}-no-compat32.run"
        '0001-Fix-conftest-to-ignore-implicit-function-declaration.patch'
        '0002-Fix-conftest-to-use-a-short-wchar_t.patch'
        '0003-Fix-conftest-to-use-nv_drm_gem_vmap-which-has-the-se.patch')
sha256sums=('4a4b2f1a0e5f4403dcc94b5df9970cb064fccee44e82282e6f03dbde6e4dfff0'
            'eafd8a3c9740f34c8a0ccd0942d05318be94889eeb64ff66c54c8d8524ff5fd0'
            'aad55ebe45fca932ebeea5071bde489d3533bcccb3fe16995c8e70929b62e01a'
            '2339209c742bf58e5aa1e5c369e925f0c78eeb74537288183b683882ebf78809')

_pkg="NVIDIA-Linux-x86_64-${pkgver}-no-compat32"

prepare() {
    sh "${_pkg}.run" --extract-only

    cd "${_pkg}"

    # GCC 14 patches
    pushd kernel
    patch -Np1 -i "${srcdir}"/0001-Fix-conftest-to-ignore-implicit-function-declaration.patch
    patch -Np1 -i "${srcdir}"/0002-Fix-conftest-to-use-a-short-wchar_t.patch
    patch -Np1 -i "${srcdir}"/0003-Fix-conftest-to-use-nv_drm_gem_vmap-which-has-the-se.patch
    popd
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
