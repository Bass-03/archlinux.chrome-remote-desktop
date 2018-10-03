# Maintainer: Fredy García <frealgagu at gmail dot com>
# Contributor: klepz <felipe.junger@aluno.ufabc.edu.br>
# Contributor: Dave Blair <mail@dave-blair.de>
# Contributor: James An <james@jamesan.ca>
# Contributor: Mateus Rodrigues Costa <charles [dot] costar [at] gmail [dot] com>

pkgname=chrome-remote-desktop
pkgver=70.0.3538.21
pkgrel=1
pkgdesc="Access other computers or allow another user to access your computer securely over the Internet"
arch=("x86_64")
url="https://chrome.google.com/webstore/detail/gbchcmhmhahfdphkhkmpfmihenigjmpp"
license=("BSD")
depends=("gtk3" "libxss" "nss" "python2" "python2-psutil" "xorg-server-xvfb" "xorg-setxkbmap" "xorg-xauth" "xorg-xdpyinfo")
install="${pkgname}.install"
source=("${pkgname}-${pkgver}.deb::http://dl.google.com/linux/direct/${pkgname}_current_amd64.deb"
        "${pkgname}.service" 
        "crd")
sha256sums=("dc19c4cc4b1a8223720524873327ca6b2cb2b2bbbef8a699a13ebf527b6c2c72"
            "e5da5ae89b5bc599f72f415d1523341b25357931b0de46159fce50ab83615a4b"
            "27dee2d383e6bd993fe0557d5c222fa80ab6d16d43775dedff6218713c7a1c06")

build() {
  cd "${srcdir}"

  msg2 "Extracting data from debian package"
  bsdtar -xf data.tar.xz -C .
  
  msg2 "Removing unnecessary .deb related files"
  rm -R "${srcdir}/etc/cron.daily"
  rm -R "${srcdir}/etc/init.d"
  rm -R "${srcdir}/etc/pam.d"
}

package() {
  cd "${srcdir}"

  install -d "${pkgdir}/etc"
  install -d "${pkgdir}/opt"
  
  cp -r "${srcdir}/etc/"* "${pkgdir}/etc"
  cp -r "${srcdir}/opt/"* "${pkgdir}/opt"

  msg2 "Packaging copyright file"
  install -Dm644 "${srcdir}/usr/share/doc/${pkgname}/copyright" "${pkgdir}/usr/share/licenses/${pkgname}/copyright"

  msg2 "Adding systemd user service file"
  install -Dm644 "${srcdir}/${pkgname}.service" "${pkgdir}/usr/lib/systemd/user/${pkgname}.service"

  msg2 "Adding runnable crd command"
  install -Dm755 "${srcdir}/crd" "${pkgdir}/usr/bin/crd"
  
  msg2 "Creating symlinks for Chromium compatibility"
  install -dm755 "${pkgdir}/etc/chromium/native-messaging-hosts"
  
  for _file in $(find "${pkgdir}/etc/opt/chrome/native-messaging-hosts" -type f); do
    _filename=${_file##*/}
    if [[ ! -f "/etc/chromium/native-messaging-hosts/${_filename}" ]]; then
      ln -s "/etc/opt/chrome/native-messaging-hosts/${_filename}" "${pkgdir}/etc/chromium/native-messaging-hosts/${_filename}"
    fi
  done
  
  msg2 "Setting uid root to user-session command"
  chmod u+s "${pkgdir}/opt/google/${pkgname}/user-session"
}
