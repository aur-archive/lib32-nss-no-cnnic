# $Id: PKGBUILD 98547 2013-10-13 06:10:00Z dwallace $
# Maintainer: Felix Yan <felixonmars@gmail.com>
# Contributor: Daniel Wallace <danielwallace at gtmanfred dot com>
# Contributor: kfgz <kfgz at interia pl>
# Contributor: Ionut Biru <ibiru at archlinux dot org>

_pkgbasename=nss
pkgname=lib32-${_pkgbasename}-no-cnnic
pkgver=3.16.3
pkgrel=1
pkgdesc="Mozilla Network Security Services (32-bit) - Remove Suspect CNNIC ROOT CA"
arch=('x86_64')
url="http://www.mozilla.org/projects/security/pki/nss/"

#download_url=ftp://ftp.mozilla.org/pub/security/nss/releases/
#alternative download link
#ftp://ftp.mozilla.org/pub/mozilla.org/security/nss/releases/NSS_${pkgver//./_}_RTM/src/${_pkgbasename}-${pkgver}.tar.gz

license=('MPL' 'GPL')
_nsprver=4.10.6
provides=("lib32-nss=$pkgver")
conflicts=('lib32-nss')
depends=("lib32-nspr>=${_nsprver}" 'lib32-sqlite>=3.6.17' "${_pkgbasename}" 'lib32-zlib')
makedepends=('gcc-multilib' 'perl')
options=('!strip' '!makeflags')
source=(ftp://ftp.mozilla.org/pub/security/nss/releases/NSS_${pkgver//./_}_RTM/src/${_pkgbasename}-${pkgver}.tar.gz
        nss.pc.in
        ssl-renegotiate-transitional.patch
        remove-cnnic.patch)

prepare() {
  cd "${srcdir}"/${_pkgbasename}-${pkgver}/
  
  # Adds transitional SSL renegotiate support - patch from Debian
  patch -Np3 -i "${srcdir}/ssl-renegotiate-transitional.patch"

  # Remove CNNIC
  patch -Np1 -i ../remove-cnnic.patch

  # Respect LDFLAGS
  sed -e 's/\$(MKSHLIB) -o/\$(MKSHLIB) \$(LDFLAGS) -o/' \
      -i nss/coreconf/rules.mk
}

build(){
  cd "${srcdir}"/${_pkgbasename}-${pkgver}/$_pkgbasename

  export PKG_CONFIG_PATH=/usr/lib32/pkgconfig
  export BUILD_OPT=1
  export NSS_USE_SYSTEM_SQLITE=1
  export NSS_ENABLE_ECC=1
  export NSPR_INCLUDE_DIR="`nspr-config --includedir`"
  export NSPR_LIB_DIR="`nspr-config --libdir`"
  export XCFLAGS="${CFLAGS}"

  make -C coreconf
  make -C lib/dbm
  make
}

package() {
  cd "${srcdir}"/${_pkgbasename}-${pkgver}/$_pkgbasename
  install -d "$pkgdir"/usr/lib32/pkgconfig
 
  NSS_VMAJOR=$(grep '#define.*NSS_VMAJOR' lib/nss/nss.h | awk '{print $3}')
  NSS_VMINOR=$(grep '#define.*NSS_VMINOR' lib/nss/nss.h | awk '{print $3}')
  NSS_VPATCH=$(grep '#define.*NSS_VPATCH' lib/nss/nss.h | awk '{print $3}')
 
  sed $srcdir/nss.pc.in \
  -e "s,%libdir%,/usr/lib32,g" \
  -e "s,%prefix%,/usr,g" \
  -e "s,%exec_prefix%,/usr/bin,g" \
  -e "s,%includedir%,/usr/include/nss,g" \
  -e "s,%NSPR_VERSION%,${_nsprver},g" \
  -e "s,%NSS_VERSION%,${pkgver},g" \
  > "$pkgdir/usr/lib32/pkgconfig/nss.pc"
  ln -s nss.pc "$pkgdir/usr/lib32/pkgconfig/mozilla-nss.pc"
 
 
  cd "${srcdir}"/${_pkgbasename}-${pkgver}/dist/*.OBJ/lib
  install -t "$pkgdir/usr/lib32" *.so
  install -t "$pkgdir/usr/lib32" -m644 libcrmf.a *.chk
}

sha1sums=('a1937de60e03a24526591d883bcfe31a3acc8ef4'
          'aa5b2c0aa38d3c1066d511336cf28d1333e3aebd'
          '8a964a744ba098711b80c0d279a2993524e8eb92'
          '55f7c0e770ad1cc2577749c49017f2a11849305d')
