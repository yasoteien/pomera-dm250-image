# 変更の記録

この記録は [Keep a Changelog](https://keepachangelog.com/ja/1.1.0/) の形で書きます。版は公開の日付（`YYYY.MM.DD`、同じ日の出し直しは `.1`）です。

**試験版です。無保証で、使うのは自己責任です。** キングジム、および ichinomoto 氏が ekesete.net で公開されている DM250 用 Debian 13 イメージとは関係のない、非公式の物です（README の「免責」）。

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
