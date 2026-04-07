# Maintainer: William Gathoye <william + aur at gathoye dot be>
# Maintainer: Hyacinthe Cartiaux <hyacinthe.cartiaux@free.fr>
# Contributor: Giorgio Gilestro <giorgio at gilest dot ro>
# Contributor: Richard Mathot <rim at odoo dot com>
_pkgbase=r8152
pkgname=${_pkgbase}-dkms
pkgver=2.21.4
pkgrel=4
pkgdesc='A kernel module for Realtek RTL8152/RTL8153/RTL8154/RTL8156 Based USB Ethernet Adapters'
url='http://www.realtek.com'
license=('GPL-2.0-only')
arch=('i686' 'x86_64' 'aarch64')
depends=('glibc' 'dkms')
install=${pkgname}.install
conflicts=("${_pkgbase}")
optdepends=('linux-headers: Build the module for Arch kernel'
            'linux-lts-headers: Build the module for LTS Arch kernel')
source=(
    "${_pkgbase}-${pkgver}.tar.gz::https://github.com/wget/realtek-r8152-linux/archive/refs/tags/v${pkgver}.tar.gz"
    'dkms.conf'
    '01-usb-transport-reliability.patch'
    '02-link-state-management.patch'
    '03-r8156b-chip-fixes.patch'
    'r8152-dkms.conf'
    '51-usb-realtek-net-pm.rules'
)
sha512sums=('6617cf7cb50fd35b97b5e3f2e47118f30af06654a809bf6b35d24a039591ab960b5ce54696fd67ae57955a5184904fdc9ad4c06c332aa9ce26c7b33fb30a4a12'
            '04106a1c3c260a9d3626eca83bd28d073d4c19f1e69b25f06ce115e3dccde678b99d0e41ce7d1adf3520c80923f6804c47a53207325ed7faa0cbe712367f80d4'
            '69e5b21688c3ab718669aca9ed2748cae4bceb9183e2c373bfdb6b7eaadc98873cf59b9ca3fee69bd8c5c8dde6e9dd560020d8f8188123bf7a5d738332776e7d'
            '9dbc7d7bef8e6d734fb645d2e25d736246a3bc521e09ea27a35ea5640ae3c989024ecc78078116147d853c0ba88d36055b6e22839e2f5a8ad6c2905c2aa024a1'
            '54d1e87832e88b20abb75f6a3ec3a57a9a5db0890e1dcd50aa02499bb2ea1adc756919ff05b85c22d4f9bc2b3e6e2d3376cc8010edb32ee9c50159a860537e6e'
            'bb83aa24358de0d93406b23936d930f3ec66041c8211bbcebf8b50e9a8ac45efda9d52900aba10a4bff4396a22dce3e3e74f426dea733c86389fcb24cf6ebf03'
            'f00574eb5e79bca7164305c36260995e5973ee4d4535ebc98940da218a45ea63375aed3c9c985b26354c19f5c327c0b9a7698004b3817417f2784e4ccad66517')

prepare() {
    cd "realtek-${_pkgbase}-linux-${pkgver}"
    patch -Np1 -i "${srcdir}/01-usb-transport-reliability.patch"
    patch -Np1 -i "${srcdir}/02-link-state-management.patch"
    patch -Np1 -i "${srcdir}/03-r8156b-chip-fixes.patch"
}

package() {
    install -Dm644 dkms.conf "${pkgdir}/usr/src/${_pkgbase}-${pkgver}/dkms.conf"

    sed -e "s/@PKGNAME@/${_pkgbase}/g" \
        -e "s/@PKGVER@/${pkgver}/g" \
        -i "${pkgdir}/usr/src/${_pkgbase}-${pkgver}/dkms.conf"

    cp -dr --no-preserve='ownership' "realtek-${_pkgbase}-linux-${pkgver}" "${pkgdir}/usr/src/${_pkgbase}-${pkgver}/src"
    install -Dm644 "realtek-${_pkgbase}-linux-${pkgver}/50-usb-realtek-net.rules" "${pkgdir}/usr/lib/udev/rules.d/50-usb-realtek-net.rules"
    install -Dm644 51-usb-realtek-net-pm.rules "${pkgdir}/usr/lib/udev/rules.d/51-usb-realtek-net-pm.rules"
    install -Dm644 r8152-dkms.conf "${pkgdir}/usr/lib/modprobe.d/r8152-dkms.conf"
}
