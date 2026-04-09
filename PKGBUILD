# Maintainer: William Gathoye <william + aur at gathoye dot be>
# Maintainer: Hyacinthe Cartiaux <hyacinthe.cartiaux@free.fr>
# Contributor: Giorgio Gilestro <giorgio at gilest dot ro>
# Contributor: Richard Mathot <rim at odoo dot com>
_pkgbase=r8152
pkgname=${_pkgbase}-dkms
pkgver=2.21.4
pkgrel=13
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
    '04-datapath-resume-hardening.patch'
    '05-round3-r8156b-fixes.patch'
    'r8152-dkms.conf'
    '51-usb-realtek-net-pm.rules'
)
sha512sums=('6617cf7cb50fd35b97b5e3f2e47118f30af06654a809bf6b35d24a039591ab960b5ce54696fd67ae57955a5184904fdc9ad4c06c332aa9ce26c7b33fb30a4a12'
            '04106a1c3c260a9d3626eca83bd28d073d4c19f1e69b25f06ce115e3dccde678b99d0e41ce7d1adf3520c80923f6804c47a53207325ed7faa0cbe712367f80d4'
            'd682056f0b5d85b9bd5030ed5f825b8457b4b9dc52d229104eef3f2072849a7958eeec926f3024d28105b49d861038ae3348667b58ef3aa1ae166444ef05f851'
            '02a44f729b7085677e083246b6b200a45767cae7df1d232b54af659e76d49972f3c108a10d9c85484b1b89e15cf790f239484d19ea1ed3fc72c413828f60f0d5'
            'f75ee4d772066e0fb0fca378c8526a4287cbb13bd8f8dabe9ec9678bf8d26028250f6dee3bb380d309cd2d2aa1503b329ce6df6c630c4a385b7698cfe97eba9c'
            'cd10f3818c1dc5720a2b57b9065acb130ac30f1345094b79b27b87841862e6f9d5568903fd44bdadc1d3bf386e201253e471a45313a8e92420e7eb5848ca5d5b'
            'f7ecb1f4ca583730d239a04661af172069c049419cb04aa97466b32ce4417b73ae6189872d9ea37c93a5347a22324ed60e02f946bf52a243cce2618aad2c8397'
            '69283770ae3778965df2f53b8d1e8ee4f0783549fc2a8aaf52f2f3c2c0a9f7b4b062befa7010708335c12d5f2110d8a8d77db6d8ee7c1a853d137047abd022be'
            'f00574eb5e79bca7164305c36260995e5973ee4d4535ebc98940da218a45ea63375aed3c9c985b26354c19f5c327c0b9a7698004b3817417f2784e4ccad66517')

prepare() {
    cd "realtek-${_pkgbase}-linux-${pkgver}"
    patch -Np1 -i "${srcdir}/01-usb-transport-reliability.patch"
    patch -Np1 -i "${srcdir}/02-link-state-management.patch"
    patch -Np1 -i "${srcdir}/03-r8156b-chip-fixes.patch"
    patch -Np1 -i "${srcdir}/04-datapath-resume-hardening.patch"
    patch -Np1 -i "${srcdir}/05-round3-r8156b-fixes.patch"
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
