# Maintainer: Jonathan Steel <jsteel at aur.archlinux.org>
# Contributor: Ido Rosen <ido@kernel.org>
# Contributor: Brett Hoerner <brett@bretthoerner.com>
# Contributor: Jochen Schalanda <jochen+aur@schalanda.name>
# Contributor: Mathieu Clabaut <mathieu.clabaut@gmail.com>
# Contributor: helios <aur@wiresphere.de>
# Contributor: George Ornbo <gornbo@gmail.com>
# Contributor: Niklas Heer <niklas.heer@me.com>

pkgname=vagrant
pkgver=1.5.1
pkgrel=1
pkgdesc="Build and distribute virtualized development environments"
arch=('any')
url="http://vagrantup.com"
license=('MIT')
depends=('puppet')
options=('!emptydirs')
source=(https://github.com/mitchellh/$pkgname-installers/archive/master.tar.gz)
md5sums=(7fa3bf05ea9b01eba10512252ab089e7)

build() {
  cd "$srcdir"/$pkgname-installers-master

  # TODO: Are the use of sudo and puppet frowned upon within AUR packages?
  sudo "$srcdir"/$pkgname-installers-master/substrate/run.sh $srcdir
  SUBSTRATE_PATH=$srcdir/substrate_archlinux_x86_64.zip

  TMP_DIR=$(mktemp -d tmp.XXXXXXXXXX)
  TMP_DIR=$(cd ${TMP_DIR}; pwd)
  export TMPDIR="${TMP_DIR}"

  # Copy the substrate and unzip it
  SUBSTRATE_TMP_DIR=$(mktemp -d tmp.XXXXXXXXXX)
  cp $SUBSTRATE_PATH ${SUBSTRATE_TMP_DIR}/substrate.zip
  pushd $SUBSTRATE_TMP_DIR
  unzip substrate.zip
  popd
  SUBSTRATE_DIR=$(cd ${SUBSTRATE_TMP_DIR}/substrate; pwd)

  # Install Vagrant
  # TODO: Is there a better way to store the revision? Or a way to quickly fetch it from github
   VAGRANT_REVISION=6977e93ba98fd19112b1fed74dafb8619f581984
  "$srcdir"/$pkgname-installers-master/package//support/install_vagrant.sh \
      ${SUBSTRATE_DIR} ${VAGRANT_REVISION} ${TMPDIR}/vagrant_version

  # Copy build into expected location
  VAGRANT_VERSION=$(cat ${TMPDIR}/vagrant_version)
  COMPILED_DIR="$srcdir"/$pkgname-$VAGRANT_VERSION
  [ -d "$COMPILED_DIR" ] && rm -rf "$COMPILED_DIR"
  cp -r "$SUBSTRATE_DIR" "$COMPILED_DIR"

  # Clean up tmp dirs
  rm -rf "$TMP_DIR"  "$SUBSTRATE_TMP_DIR"
}

package() {
  cd "$srcdir"/$pkgname-$pkgver

  mkdir -p "$pkgdir"/usr/local
  INSTALL_DIR="$pkgdir"/usr/local/$pkgname
  mv "$srcdir"/$pkgname-$pkgver/ "$INSTALL_DIR"

  # Create the executable proxy
  mkdir "$pkgdir"/usr/bin
  EXECUTABLE_PATH="$pkgdir"/usr/bin/$pkgname
  cat <<EOF >"$EXECUTABLE_PATH"
#!/usr/bin/env bash
#
# This script just forwards all arguments to the real vagrant binary.

"$INSTALL_DIR"/bin/vagrant "\$@"
EOF
  chmod +x "$EXECUTABLE_PATH"
}
