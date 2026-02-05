#This file is heavily based on the one originally made by frankenstein91
#Maintainer: Your Name <youremail@domain.com>
pkgname=deskhpsdr-git
_pkgname=deskhpsdr
pkgver=2.6.77.r48.g3a2cae8
pkgrel=1
pkgdesc="A GTK+/Linux based client for the HPSDR and Hermes-Lite SDR"
arch=('i686' 'x86_64' 'armv7h' 'aarch64')
url="https://github.com/dl1bz/deskhpsdr"
license=('GPL3')
depends=(
    'gtk3'
    'webkit2gtk-4.1'
    'fftw'
    'alsa-lib'
    'openssl'
    'curl'
    'libusb'
    'i2c-tools'
    'libgpiod'
    'libpulse'
    'libpcap'
    'json-c'
    'gnome-themes-extra'
    'libaio'
    'avahi'
    'libiio'
    'rtl-sdr'
    'soapysdr'
    'zstd'
    'python'
)
makedepends=('git' 'cmake' 'gcc-fortran' 'cppcheck' 'dos2unix' 'wget' 'meson' 'ninja' 'clang' 'llvm')
optdepends=('libad9361-iio: for AD9361 support')
provides=("${_pkgname}")
conflicts=("${_pkgname}")
source=("${_pkgname}::git+${url}.git")
sha256sums=('SKIP')

pkgver() {
  cd "${_pkgname}"
  git describe --long --tags | sed 's/\([^-]*-g\)/r\1/;s/-/./g'
}

prepare() {
  cd "${srcdir}/${_pkgname}"
  #This next line was needed for deskhpsdr to compile properly. I wonder if it's still needed?
  sed -i 's/CFLAGS?=/CFLAGS+=/' wdsp-1.29/Makefile
  sed -i 's|Exec=/usr/local/bin/deskhpsdr|Exec=/usr/bin/deskhpsdr|' LINUX/deskHPSDR.desktop
  sed -i 's|Icon=/usr/local/share/deskhpsdr/trx_icon.png|Icon=deskhpsdr|' LINUX/deskHPSDR.desktop
  #This next line isn't the best way to do this, but it works, so it's good enough
  sed -i 's/^.*sudo.*$/echo "this is not debian"/' update_libs.sh
  #I'm actually not entirely sure if I should be making this next change LOL
  sed -i 's|make -j $CPU_CORES -l 4|make -j $CPU_CORES|' ./build-rigctld.sh

}

build() {
  cd "${srcdir}/${_pkgname}"
 #I found a few programs that need this rigctl support, so I'm building it. I think FreeDATA or FreeDV needs it. One of the two.
  ./build-rigctld.sh
  #Update libs apparently has to run here or makepkg will claim that the build fails when the script ends.
  ./update_libs.sh
  # Create the make.config.deskhpsdr file
  cat > make.config.deskhpsdr <<-EOF
		TCI=ON
		GPIO=OFF
		MIDI=ON
		SATURN=OFF
		USBOZY=OFF
		SOAPYSDR=OFF
		STEMLAB=OFF
		TTS=ON
		AUDIO=PULSE
		ATU=OFF
		COPYMODE=OFF
		AUTOGAIN=ON
		REGION1=OFF
		WMAP=OFF
		EQ12=OFF
		DEVEL=OFF
		TAHOEFIX=ON
	EOF
  make
}

package() {
  cd "${srcdir}/${_pkgname}"
  install -Dm755 "${_pkgname}" "${pkgdir}/usr/bin/${_pkgname}"
  install -Dm755 LINUX/rigctld_deskhpsdr "${pkgdir}/usr/bin/rigctld_deskhpsdr"
  install -Dm644 "release/${_pkgname}/hpsdr_icon.png" "${pkgdir}/usr/share/icons/hicolor/256x256/apps/deskhpsdr.png"
  install -Dm644 "LINUX/deskHPSDR.desktop" "${pkgdir}/usr/share/applications/deskhpsdr.desktop"
}
