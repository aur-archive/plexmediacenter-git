# Maintainer: Daniel Wallace daniel.wallace@gatech.edu
pkgname=plexmediacenter-git
pkgver=20130205
pkgrel=1
pkgdesc="Plex Media Center on Linux"
arch=('x86_64' 'i686')
url="http://forums.plexapp.com/index.php/topic/34481-the-state-of-plex-media-center-on-linux/"
license=('GPL')
depends=('alsa-lib' 'boost' 'boost-libs' 'cmake' 'curl' 'cvs' 'dbus-core' 'enca' 'faac' 'faad2' 'fontconfig' 'freetype2' 'fribidi' 'gd' 'glew' 'gperf' 'jasper' 'ldc' 'libass' 'libbluray' 'libcdio' 'libgl' 'libjpeg-turbo' 'libmad' 'libmicrohttpd' 'libmms' 'libmodplug' 'libmpeg2' 'libmysqlclient' 'libogg' 'libpng' 'libpulse' 'libsamplerate' 'libssh' 'libva' 'libvorbis' 'libxinerama' 'libxmu' 'libxrandr' 'libxrender' 'libxt' 'libxtst' 'lsb-release' 'lzo2' 'mesa' 'mesa-demos' 'moc' 'nasm' 'octave' 'openssl' 'pcre' 'pion-net' 'pmount' 'python' 'python2-pysqlite' 'qmmp' 'rtmpdump' 'samba' 'sdl' 'sdl_gfx' 'sdl_image' 'sdl_mixer' 'smbclient' 'sqlite3' 'unzip' 'vlc' 'wavpack' 'xineramaproto' 'xorg-xdpyinfo' 'zip' )
makedepends=('git' 'mingw32-binutils' 'openssh')
provides=(pmc)
conflicts=(xbmc plexmediaclient-git)
replaces=(xmbc plexmediaclient-git)
source=(0004-remove-lboost_thread-mt.patch
        0005-ssh-for-submodules-is-stupid.patch)

_gitroot=https://github.com/gewalker/plex-linux.git
_gitname=laika

build() {
  cd "$srcdir"
  msg "Connecting to GIT server...."

  if [[ -d "$_gitname" ]]; then
    cd "$_gitname" && git pull origin
    msg "The local files are updated."
  else
    git clone "$_gitroot" "$_gitname"
  fi

  msg "GIT checkout done or server timeout"
  msg "Starting build..."

  rm -rf "$srcdir/$_gitname-build"
  git clone  "$srcdir/$_gitname" "$srcdir/$_gitname-build"
  cd "$srcdir/$_gitname-build"

  patch -Np1 -i "$srcdir/${source[1]}"

  git submodule init
  git submodule update

  patch -Np1 -i "$srcdir/${source[0]}"

  find . -name configure.ac -exec sed -i 's/AM_CONFIG_HEADER/AC_CONFIG_HEADERS/g' {} +

  ./bootstrap
  ./configure --prefix=/usr

  numcpu="$(lscpu|awk '$1=="CPU(s):" {print $2}')"
  [[ $MAKEFLAG != *-j[1-9]* ]] && MAKEFLAGS="-j$((numcpu + 1)) $MAKEFLAGS"

  make 
}

package() {
  cd "$srcdir/$_gitname-build"
  make DESTDIR="$pkgdir" install
  sed 's/python/&2/' "$pkgdir/usr/bin/pmc"
}

# vim:set ts=2 sw=2 et:
md5sums=('dbbf54314dfab442046e481dac3407a1'
         '724007aabbf2917ff7770cd5fc509d20')
