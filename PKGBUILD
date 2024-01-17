# Maintainer: Robin Jadoul (aur@ur4ndom.dev) 
# Contributor: Wojciech Kępka (wojciech@wkepka.dev) 
pkgname=helix-git
_pkgname=helix
pkgver=23.10.r205.g44cb8e547
pkgrel=1
pkgdesc="A text editor written in rust"
url="https://helix-editor.com"
_git="https://github.com/helix-editor/${_pkgname}.git"
arch=(x86_64)
makedepends=('git' 'cargo')
depends=()
provides=('hx')
conflicts=('helix')
source=("${_pkgname}::git+${_git}")
sha256sums=('SKIP')

_bin="hx"
_lib_path="/usr/lib/${_pkgname}"
_rt_path="${_lib_path}/runtime"


pkgver() {
    cd "${_pkgname}"
    git describe --long --tags | sed 's/\([^-]*-g\)/r\1/;s/-/./g'
}

prepare() {
    cat > "$_bin" << EOF
#!/usr/bin/env sh
HELIX_RUNTIME=${_rt_path} exec ${_lib_path}/${_bin} "\$@"
EOF
    chmod +x "$_bin"

    cd "${_pkgname}"
    git submodule update --force --init --recursive --depth 1 --jobs 8
    cargo fetch --locked --target "$CARCH-unknown-linux-gnu"
}

build() {
    cd "${_pkgname}"
    export CARGO_TARGET_DIR=target
    cargo build --frozen --release
}

check() {
    cd "${_pkgname}"
    cargo test --frozen --all-features --workspace
}

package() {
    cd "${_pkgname}"
    mkdir -p "${pkgdir}${_lib_path}"
    rm -r  "runtime/grammars/sources" 
    cp -r "runtime" "${pkgdir}${_lib_path}"
    install -Dm 0755 "target/release/${_bin}" "${pkgdir}${_lib_path}/${_bin}"
    install -Dm 0644 "LICENSE" "${pkgdir}/usr/share/licenses/${_pkgname}/LICENSE"
    install -Dm 0777 "${srcdir}/${_bin}" "${pkgdir}/usr/bin/${_bin}"
    install -Dm 0644 "contrib/Helix.desktop" "${pkgdir}/usr/share/applications/Helix.desktop"
    install -Dm 0644 "contrib/Helix.appdata.xml" "${pkgdir}/usr/share/appdata/Helix.appdata.xml"
    install -Dm 0644 "contrib/helix.png" "${pkgdir}/usr/share/icons/Helix.png"
    install -Dm 0644 "contrib/completion/hx.zsh" "${pkgdir}/usr/share/zsh/site-functions/_hx"
    install -Dm 0644 "contrib/completion/hx.bash" "${pkgdir}/usr/share/bash-completion/completions/hx.bash"
    install -Dm 0644 "contrib/completion/hx.fish" "${pkgdir}/usr/share/fish/vendor_completions.d/hx.fish"
}
