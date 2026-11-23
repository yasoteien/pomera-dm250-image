# 中身の出どころとライセンス（NOTICE）

このイメージ（`pomera-dm250-trixie-<版>.img.xz`）に入っている物の出どころです。英語の要約は最後にあります。

## 1. Debian の包み

- rootfs の包みは、Debian 13（trixie、armhf）の公式の置き場（trixie・trixie-updates・trixie-security の `main` と `non-free-firmware`）から入れた物で、中身は変えていません。
- 入っている包みと版は、イメージの中で `dpkg-query -W` を打つと出ます。それぞれのライセンスは、イメージの中の `/usr/share/doc/<包みの名前>/copyright` にあります。
- ソース: Debian の置き場（`apt-get source <包みの名前>`）、または同じ版を https://snapshot.debian.org/ から取れます。

## 2. 自前のカーネルと U-Boot

- Linux カーネル 6.18.64（Linux 6.18 LTS ＋ DM250 用のパッチ）、デバイスツリー（RSCE）、U-Boot、`pomera-kernel-tools` のソースは https://github.com/yasoteien/pomera-kernel にあります。以後の更新は、apt の置き場（https://yasoteien.github.io/pomera-kernel-apt/ ）から入ります。
- ライセンス: カーネルとパッチは GPL-2.0、U-Boot は GPL-2.0+、台本は GPL-2.0-or-later。詳しくは pomera-kernel の README と LICENSE、デバイスツリーの出どころも同じ README にあります。

## 3. FDTP

- FDTP 1.18 は https://github.com/yasoteien/fdtp の公開の Release の armhf の .deb を、sha256 で照らしてそのまま入れています。ソースとライセンスはそのリポジトリと、イメージの中の `/usr/share/doc/fdtp/copyright` にあります。
- その .deb に入っている `fdtp-dict`（純正の電子辞書を読む道具）は、ソースを公開していないバイナリです。FDTP の本体とは別のプログラムで、ライセンスも別です（同じ copyright のファイルに書いています）。

## 4. Wi-Fi と Bluetooth のファームウェア

- Wi-Fi の本体のファームウェアは Debian の `firmware-brcm80211`（non-free-firmware）です。
- 基板の NVRAM（`brcmfmac43430-sdio.txt`）と Bluetooth の HCD（`BCM43430A1.hcd`）は、Armbian の firmware（https://github.com/armbian/firmware 、commit 2a9e1c19460401443267926181191d57e3ff175d）から取り、sha256 で照らして置いています。出どころと sha256 はイメージの中の `/usr/share/doc/pomera-image-firmware/NOTICE` にあります。

## 5. SD カードの頭の起動の部品

SD カードのセクタ 64 から（69632 バイト、セクタ 64〜199）に、Rockchip の DRAM の初期化と miniloader（8MiB に置いた U-Boot を読む物）を置いています。Rockchip が公開している rockchip-linux/rkbin の部品を、U-Boot の `mkimage -n rk3128 -T rksd` で組んだ物です。ほかで配られているイメージの部品は使っていません。

| 部品 | rkbin の中の場所 | sha256 |
|---|---|---|
| DDR の初期化 | `bin/rk31/RK3128_DDR_300M_V2.04.bin` | 0251159c…（全桁: 0251159cdbec4b2ece18f78954e9418829d9d79d46d084c542bdbe3e74869b3b） |
| miniloader | `bin/rk31/rk312xminiloaderall.bin` | 18969550…（全桁: 18969550f479d99aeac85084da4df6f3fd1dcdfee5b63c7058ccd5a3af15eb9b） |
| 組んだ物（セクタ 64〜199） | — | f41b3ee5…（全桁: f41b3ee590360499321991a5fbef850d70b96e6d6d71a1b4a9f2cfde416a89e3） |

- 出どころ: https://github.com/rockchip-linux/rkbin の commit 97bcee07（2015-05-15、`RKBOOT/RK3128MINIALL.ini` の VERSION 2.20 が指す物）。
- rkbin の LICENSE の要点: 使う・写す・配る事を許し、逆コンパイル等と、著作権の表示を消す事を禁じています。この LICENSE が rkbin に入ったのは 2023-07-17 で、上の 2 つの部品はそれより前に木から消えているため、文面が及ぶかは分かりません（部品は今も rkbin の公開の履歴から取れます）。
- rkbin の LICENSE の全文:

```text
Copyright © 2017-2023, Rockchip Electronics Co., Ltd. All rights reserved.

BY DOWNLOADING, COPYING, SAVING OR OTHERWISE USING THIS SOFTWARE, YOU
ACKNOWLEDGE THAT YOU AGREE THE SOFTWARE RECEIVED FROM ROCKCHIP IS PROVIDED TO
YOU ON AN "AS IS" BASIS and ROCKCHIP DISCLAIMS ANY AND ALL WARRANTIES AND
REPRESENTATIONS WITH RESPECT TO SUCH SOFTWARE, WHETHER EXPRESS, IMPLIED,
STATUTORY OR OTHERWISE, INCLUDING WITHOUT LIMITATION, ANY IMPLIED WARRANTIES OF
TITLE, NON-INFRINGEMENT, MERCHANTABILITY, SATISFACTORY QUALITY, ACCURACY OR
FITNESS FOR A PARTICULAR PURPOSE. IN NO EVENT SHALL ROCKCHIP BE LIABLE FOR ANY
DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES
HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT
LIABILITY, OR TORT ARISING IN ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF
ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.

Rockchip hereby grants to you a non-exclusive license (a) to use, copy,
distribute the Software; (b) to modify any source code as part of Software (if
any) and sublicense, distribute such modifications.
Except as expressly authorized by Rockchip in writing, you may NOT:
(a) decompile, reverse-engineer, disassemble, or attempt to derive any source
code from the Software;
(b) remove or obscure any copyright, patent, or trademark statement or notices
contained in the Software.
```

（2026-11-23 に https://github.com/rockchip-linux/rkbin の LICENSE から写した物）


## 6. このイメージの自前の台本と設定

最初の起動の台本（区画を広げる・利用者と Wi-Fi の設定・内蔵メモリと辞書を当てる）、`pomera-ssh`、`pomera-dpkg-recover`、ログインの案内、設定のファイルは、yasoteien が書いた物で、GPL-2.0-or-later です。イメージの中の台本そのものがソースです（多くのファイルの頭に SPDX の行があります）。

---

## Summary (English)

- Debian packages: unmodified Debian 13 (trixie, armhf) packages from the official archive (`main`, `non-free-firmware`). List them with `dpkg-query -W`; licenses are in `/usr/share/doc/<package>/copyright`; sources via `apt-get source` or https://snapshot.debian.org/.
- Kernel, device tree, U-Boot, pomera-kernel-tools: sources at https://github.com/yasoteien/pomera-kernel (GPL-2.0 / GPL-2.0+ / GPL-2.0-or-later); later updates come from its apt repository.
- FDTP 1.18: the public armhf .deb from https://github.com/yasoteien/fdtp, installed unchanged (checked by sha256). It includes `fdtp-dict`, a separate binary whose source is not published.
- Wi-Fi NVRAM and Bluetooth HCD: from Armbian firmware (commit 2a9e1c19460401443267926181191d57e3ff175d); see `/usr/share/doc/pomera-image-firmware/NOTICE` in the image.
- Boot parts at sector 64: `RK3128_DDR_300M_V2.04.bin` and `rk312xminiloaderall.bin` from rockchip-linux/rkbin commit 97bcee07, packed with U-Boot `mkimage -n rk3128 -T rksd`. Hashes and the rkbin LICENSE are in section 5 above.
- The image's own scripts and settings: GPL-2.0-or-later by yasoteien; the scripts in the image are the source.
