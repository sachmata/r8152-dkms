# Maintainer: William Gathoye <william + aur at gathoye dot be>
# Maintainer: Hyacinthe Cartiaux <hyacinthe.cartiaux@free.fr>
# Contributor: Giorgio Gilestro <giorgio at gilest dot ro>
# Contributor: Richard Mathot <rim at odoo dot com>
_pkgbase=r8152
pkgname=${_pkgbase}-dkms
pkgver=2.21.4
pkgrel=2
pkgdesc='A kernel module for Realtek RTL8152/RTL8153/RTL8154/RTL8156 Based USB Ethernet Adapters'
url='http://www.realtek.com'
license=('GPL-2.0-only')
arch=('i686' 'x86_64' 'aarch64')
depends=('glibc' 'dkms')
conflicts=("${_pkgbase}")
optdepends=('linux-headers: Build the module for Arch kernel'
            'linux-lts-headers: Build the module for LTS Arch kernel')
source=(
    "${_pkgbase}-${pkgver}.tar.gz::https://github.com/wget/realtek-r8152-linux/archive/v${pkgver}.tar.gz"
    'dkms.conf'
    'stability-fixes.patch'
    'r8152-dkms.conf'
    '51-usb-realtek-net-pm.rules'
)
sha512sums=('6617cf7cb50fd35b97b5e3f2e47118f30af06654a809bf6b35d24a039591ab960b5ce54696fd67ae57955a5184904fdc9ad4c06c332aa9ce26c7b33fb30a4a12'
            '04106a1c3c260a9d3626eca83bd28d073d4c19f1e69b25f06ce115e3dccde678b99d0e41ce7d1adf3520c80923f6804c47a53207325ed7faa0cbe712367f80d4'
            '84f4c05f19e044d7a6a74d31f22c9999b6625e1876e2d4444f62f3f968f3dcc8b7e7ced9361982cd1cd67dca7c525111dbe617b6732e6614e39dd2c2f13c5246'
            'bb83aa24358de0d93406b23936d930f3ec66041c8211bbcebf8b50e9a8ac45efda9d52900aba10a4bff4396a22dce3e3e74f426dea733c86389fcb24cf6ebf03'
            '30ffac51959b8d5f48ebab3bc83e1f4981550633d35a79dc2fc01b2a26b0756ec9a99e9d805c94ef66d1f8a787eead1871fd391b962e17ac957994b65712f545')

prepare() {
    cd "realtek-${_pkgbase}-linux-${pkgver}"
    patch -Np1 -i "${srcdir}/stability-fixes.patch"
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
