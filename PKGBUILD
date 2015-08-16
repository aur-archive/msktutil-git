# Maintainer: Luke Mundy <lmundy@gmail.com>
# Contributor: Ricardo Wurmus <ricardo.wurmus@gmail.com>
#
# Ricardo Wurmus was the original Maintainer of this package, I have
# taken ownership of the orphaned package in order to fix it.

pkgname=msktutil-git
pkgver=20130801
pkgrel=1
pkgdesc="An Active Directory Keytab Manager"
arch=('i686' 'x86_64')
url="http://fuhm.net/software/msktutil/"
license=('GPL2')
depends=('krb5' 'openldap' 'libsasl')
makedepends=('git')
conflicts=('msktutil')
provides=('msktutil')

_gitroot="http://repo.or.cz/r/msktutil.git"
_gitname="msktutil"

build() {
    cd "$srcdir"
    msg "Connecting to GIT server..."

    if [ -d $_gitname ] ; then
        cd $_gitname && git pull origin
        msg "The local files are updated."
    else
        git clone $_gitroot $_gitname
    fi

    msg "Starting make..."

    rm -rf "$srcdir/$_gitname-build"
    git clone "$srcdir/$_gitname" "$srcdir/$_gitname-build"
    cd "$srcdir/$_gitname-build"

    # Sometimes the configure script is missing
    [ ! -e configure ] && autoconf

    # patch the path for the header com_err.h
    sed -i -e 's|<com_err\.h|<et/com_err\.h|' configure msktutil.h
    # additionally link with -lcom_err and -lresolv
    sed -i -e '/^LIBS/s|$| -lcom_err -lresolv|' Makefile.in

    # linking is broken with --as-needed flag
    export LDFLAGS="${LDFLAGS%%-Wl,--as-needed}" 
    export CXXFLAGS=""

    ./configure --prefix=/usr
    make 
}

package() {
    cd "$srcdir/$_gitname-build"
    make DESTDIR=${pkgdir} install || return 1
}

