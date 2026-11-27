# 変更の記録

この記録は [Keep a Changelog](https://keepachangelog.com/ja/1.1.0/) の形で書きます。版は公開の日付（`YYYY.MM.DD`、同じ日の出し直しは `.1`）です。

**試験版です。無保証で、使うのは自己責任です。** キングジム、および ichinomoto 氏が ekesete.net で公開されている DM250 用 Debian 13 イメージとは関係のない、非公式の物です（README の「免責」）。

## [2026.11.28] - 2026-11-28

**お詫びと修正の版です。** 2026.11.23 の版には、次の不具合がありました。ご迷惑をおかけし、申し訳ありません。

### 直した事

- **XFCE が起動しませんでした。** `startx` が「Cannot run in framebuffer mode」で止まっていました。DM250 には画面用（rockchip-drm）と GPU 用（lima）の 2 つの DRM があり、X の設定で画面の方を指していなかったためです。`/etc/X11/xorg.conf.d/20-pomera-modesetting.conf` に `Option "kmsdev" "/dev/dri/card0"` を足しました。
- **XFCE でのログインの知らせの窓が文字化けしていました。** 窓（xmessage）は日本語を表示できないため、英語で出すようにしました（端末での知らせは今までどおり日本語）。
- ログインのたびに journal に出ていた fluidsynth の起動の失敗を止めました（`fluidsynth.service` の利用者単位を mask。音には関係しません）。

### 変えた事

- **普段の swap を、SD カードからメモリを圧縮する zram（384MB、lzo-rle）に変えました。** SD カードへの swap は極端に遅く、メモリが足りなくなると本体が固まる事がありました。zram では、メモリが足りなくなっても動きが大きくは落ちません。それでも足りない時は、固まる代わりにアプリが閉じられます。
- SD カードの末尾の swap（1GiB）は、hibernate の写しを書く時だけ使うようにしました（普段は使いません）。
- カーネルを 6.18.65（pomera-kernel 2026.11.24.1）にしました。

### 2026.11.23 の版を使っている方へ

- 焼き直さなくても、X だけなら次の 1 行で直ります（端末で流してから `startx`）:
  `printf 'Section "Device"\n\tIdentifier "pomera-drm"\n\tDriver "modesetting"\n\tOption "kmsdev" "/dev/dri/card0"\nEndSection\n' | sudo tee /etc/X11/xorg.conf.d/20-pomera-modesetting.conf`
- zram などほかの変更は、この版を焼き直すと入ります。

### English

- **Apology and fix release.** The 2026.11.23 image had bugs. We are sorry for the trouble.
- Fixed: **XFCE did not start** (`startx` stopped with "Cannot run in framebuffer mode"). The X config now points at the display DRM device (`Option "kmsdev" "/dev/dri/card0"`). Users of 2026.11.23 can apply the same one-line fix shown above without rewriting the card.
- Fixed: the login notice window in XFCE showed garbled text (xmessage cannot show Japanese); it is now in English. Stopped the failing per-user `fluidsynth.service` at login.
- Changed: **the everyday swap is now a 384 MB compressed-memory zram (lzo-rle)** instead of the SD card, which was very slow and could freeze the machine under memory pressure. The 1 GiB swap at the end of the card is now used only for the hibernation image.
- Kernel 6.18.65 (pomera-kernel 2026.11.24.1).

## [2026.11.23] - 2026-11-23

最初の公開です。**8GB 以上の SD カードが必要です。** 焼き方・最初の起動・確かめ方は README を見てください。

### 中身

- Debian 13（trixie、armhf）。日本語の表示と入力（ja_JP.UTF-8・日本語のフォント・Mozc・DM250 の JIS 配列）、XFCE。
- 自前で組んだ Linux カーネル 6.18.64 と U-Boot（yasoteien/pomera-kernel）。以後の更新は `sudo apt update && sudo apt full-upgrade` で入ります。
- FDTP 1.18（おすすめの設定を当てた状態）。
- 最初の起動で、利用者の名前・パスワード・Wi-Fi を聞きます（共通のパスワードはありません）。`/` を SD カードいっぱいまで広げ、swap 1GiB を作ります。
- 日本版 DM250 では、本体の内蔵メモリを読み取り専用で見られるようにし、純正の辞書と ATOK の固有名詞を Mozc に取り込みます（自動）。
- ssh は既定で閉じています（`pomera-ssh on` で開く）。自動の更新はせず、ログインの時に安全の更新の件数を知らせます。

### 既知の事

- Wi-Fi は nmtui で設定します（Tab と矢印キーで動きます）。DM250 の Wi-Fi は 2.4GHz だけです。

### English

- First release. **Requires a microSD card of 8 GB or larger.** See README.en.md for writing, first boot, and verification.
- Debian 13 (trixie, armhf) with Japanese display and input, XFCE, a self-built Linux 6.18.64 kernel and U-Boot (updated via apt from yasoteien/pomera-kernel), and FDTP 1.18 with the recommended settings.
- The first boot asks for a user name, password, and Wi-Fi (no shared default password), grows `/` to fill the card, and makes a 1 GiB swap.
- ssh is off by default (`pomera-ssh on`). No automatic updates; pending security updates are counted at login.
- Known issue: Wi-Fi is set up with nmtui (Tab and arrow keys). The DM250 Wi-Fi is 2.4 GHz only.
