# Maintainer: William Gathoye <william + aur at gathoye dot be>
# Maintainer: Hyacinthe Cartiaux <hyacinthe.cartiaux@free.fr>
# Contributor: Giorgio Gilestro <giorgio at gilest dot ro>
# Contributor: Richard Mathot <rim at odoo dot com>
_pkgbase=r8152
pkgname=${_pkgbase}-dkms
pkgver=2.21.4
pkgrel=15
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
    '00-kernel-7.0-hex2bin-include.patch'
    '01-usb-transport-reliability.patch'
    '02-r8156b-chip-and-phy-fixes.patch'
    '03-lifecycle-and-suspend-resume.patch'
    'r8152-dkms.conf'
    '51-usb-realtek-net-pm.rules'
)
sha512sums=('6617cf7cb50fd35b97b5e3f2e47118f30af06654a809bf6b35d24a039591ab960b5ce54696fd67ae57955a5184904fdc9ad4c06c332aa9ce26c7b33fb30a4a12'
            '04106a1c3c260a9d3626eca83bd28d073d4c19f1e69b25f06ce115e3dccde678b99d0e41ce7d1adf3520c80923f6804c47a53207325ed7faa0cbe712367f80d4'
            '94e509bed90c82aaac5dcab24e116e53766b0bf8d322ff03fe7ee3923cf5260438ca0180098de2eb3044fc4265564d94663cd37aa1aca7837f920be04e6d8109'
            '58fab39d0caba4df33ad5b9049a09e319de20e1760d77dd5662917155a00033a852e1859d05698311dbeb17bddcdbfb1a262f803e8cacc41cf2dac691d7a0153'
            'b2ff6283f5b18c8a0188b72c007927bea50549466df3d0b62f462d6e67f0e4867f98eb56de783c45eba4a090500d3d7e6620321181b922f3909da5c528affdf9'
            '08bd7b4cd0d2907eecbeb9a4ccf5fefce5b4827dc9afee17e486da6f1c46be2f5b2ff060cc7f993dd24a79b9d3fc408a89479130da4bbd1cac415c4609e74060'
            '69283770ae3778965df2f53b8d1e8ee4f0783549fc2a8aaf52f2f3c2c0a9f7b4b062befa7010708335c12d5f2110d8a8d77db6d8ee7c1a853d137047abd022be'
            'f00574eb5e79bca7164305c36260995e5973ee4d4535ebc98940da218a45ea63375aed3c9c985b26354c19f5c327c0b9a7698004b3817417f2784e4ccad66517')

prepare() {
    cd "realtek-${_pkgbase}-linux-${pkgver}"
    patch -Np1 -i "${srcdir}/00-kernel-7.0-hex2bin-include.patch"
    patch -Np1 -i "${srcdir}/01-usb-transport-reliability.patch"
    patch -Np1 -i "${srcdir}/02-r8156b-chip-and-phy-fixes.patch"
    patch -Np1 -i "${srcdir}/03-lifecycle-and-suspend-resume.patch"
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
