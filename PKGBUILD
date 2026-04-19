# Maintainer: William Gathoye <william + aur at gathoye dot be>
# Maintainer: Hyacinthe Cartiaux <hyacinthe.cartiaux@free.fr>
# Contributor: Giorgio Gilestro <giorgio at gilest dot ro>
# Contributor: Richard Mathot <rim at odoo dot com>
_pkgbase=r8152
pkgname=${_pkgbase}-dkms
pkgver=2.21.4
pkgrel=17
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
            '825de87363287b15f71bc8d92fabb4e14e908d44d2b30634f8dd8525a9933828bbb0d73e84f11e1e32809d635cf4a2333cb90e0f90e7cb90a3ab46ae624cc1d7'
            'd664cfe27007c3bfaf1f2e4e3595c93dc3e7b273f82523910c4d10fada74f3a8457a2c819792dfaefa4c0a04ac87f5110b7807101880172bbe2a656313dda47b'
            'b72508eafb1981407ee45fc21563a13f869b87cbf12cf37706129529b4d9bd275284e79b12df0c732bee9888a776858037eed969398474e9b8890372afa0479b'
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
