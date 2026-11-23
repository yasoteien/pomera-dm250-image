# pomera-dm250-image（ポメラ DM250 用の Debian 13 の SD イメージ）

キングジムのポメラ DM250（日本版）で Debian 13（trixie）を動かすための SD カードのイメージです。SD カードに焼いて挿し、電源を入れれば起動します。

- 中身: Debian 13（trixie、armhf）、自前で組んだ Linux カーネル 6.18.64（Linux 6.18 LTS ＋ DM250 用のパッチ）と U-Boot、日本語の表示と入力（ja_JP.UTF-8・日本語のフォント・Mozc・DM250 の JIS 配列）、XFCE、FDTP 1.18（文章を書くための道具。おすすめの設定を当てた状態）。
- 対象は **DM250 の日本版だけ**です。US 版（DM250US）・DM200 では使わないでください。
- 本体の内蔵メモリ（eMMC）には書きません。SD カードを抜けば、本体の純正のソフトで起動します。
- 試験版です。下の「免責」を読んでから使ってください。

版: 2026.11.23（Release: https://github.com/yasoteien/pomera-dm250-image/releases/tag/v2026.11.23 ）

## 免責

- これは試験版です。動くこと・品質・目的に合うことは保証しません（無保証です）。使うのは自己責任でお願いします。
- これを使って、ポメラの本体・SD カード・データに不具合や損失が起きても、私（yasoteien）は責任を負いません。
- キングジム、および ichinomoto 氏が ekesete.net（https://www.ekesete.net/log/?p=9565）で公開されている DM250 用 Debian 13 イメージとは関係のない、非公式の物です。両者に問い合わせないでください。

## 用意する物

- **8GB 以上の microSD カード**（4GB のカードには焼けません）。焼くとカードの中身はすべて消えます。
- SD カードを読み書きできる PC（Windows・Mac・Linux のどれか）。
- Release の `pomera-dm250-trixie-2026.11.23.img.xz` と `SHA256SUMS`。

## 落とした物を確かめる（sha256）

`SHA256SUMS` には、`.img.xz`（落とす物）と、それを解いた `.img` の 2 行があります。落とした `.img.xz` の値が、`SHA256SUMS` の `.img.xz` の行と同じ事を確かめてください。

- Windows（PowerShell）: `Get-FileHash .\pomera-dm250-trixie-2026.11.23.img.xz -Algorithm SHA256`
- Mac: `shasum -a 256 -c --ignore-missing SHA256SUMS`
- Linux: `sha256sum -c --ignore-missing SHA256SUMS`

（`--ignore-missing` は、手元に無い `.img` の行を飛ばすためです。）この版の値は次のとおりです。

```
2610809e0e5009fb5b1c04ad75860a5177c337bfe66924cf7bf21cf26de06f19  pomera-dm250-trixie-2026.11.23.img.xz
e1693c0cb13e6f05eeb41145c5d63e179d70d5328d497189b0b6894df3a07ff3  pomera-dm250-trixie-2026.11.23.img
```

## SD カードへの焼き方

### いちばん簡単な方法（Windows・Mac・Linux 共通）: Raspberry Pi Imager

Raspberry Pi Imager（https://www.raspberrypi.com/software/ ）は `.img.xz` を解かずにそのまま焼けます。

1. Raspberry Pi Imager を入れて開く。
2. 「デバイス」は選ばなくてかまいません（「No filtering」等でよい）。
3. 「OS を選ぶ」の一覧のいちばん下の **「カスタムを使う」（Use custom）** を選び、落とした `pomera-dm250-trixie-2026.11.23.img.xz` を選ぶ。
4. 「ストレージを選ぶ」で、焼く SD カードを選ぶ。**大きさと名前を見て、ほかのディスクを選んでいない事を確かめてください。**
5. 「OS のカスタマイズ（設定を編集する）」を聞かれたら **「いいえ」** を選ぶ（このイメージは最初の起動で名前やパスワードを聞くので、Imager の設定は使いません）。
6. 書き込みと確かめ（ベリファイ）が終わったら、SD カードを取り出す。

### Linux で dd を使う方法

```
lsblk                          # SD カードの名前（例: /dev/sdX）を、大きさで確かめる
xz -dc pomera-dm250-trixie-2026.11.23.img.xz | sudo dd of=/dev/sdX bs=4M conv=fsync status=progress
```

### Mac で dd を使う方法

`xz` は Homebrew 等で入れてください（`brew install xz`）。

```
diskutil list                  # SD カードの名前（例: /dev/diskN）を、大きさで確かめる
diskutil unmountDisk /dev/diskN
xz -dc pomera-dm250-trixie-2026.11.23.img.xz | sudo dd of=/dev/rdiskN bs=4m conv=fsync
diskutil eject /dev/diskN
```

**注意: dd の `of=` の行き先を間違えると、そのディスク（PC の内蔵ディスクを含む）の中身が消えます。** 例の `sdX`・`diskN` はそのまま打たず、必ず自分の SD カードの名前に置き換えてください。迷う時は Raspberry Pi Imager を使ってください。

## 最初の起動

SD カードを DM250 に挿し、電源を入れます。

1. 自前の U-Boot で起動し、最初の起動では **SD カードの残りいっぱいまで `/` が広がり、末尾に swap（1GiB）ができます**（hibernate に使います）。数十秒かかる事があります。
2. 日本語の画面で次を聞かれます。
   - 利用者の名前とパスワード（**イメージに共通のパスワードはありません**。ここで決めた物だけが使えます）
   - Wi-Fi につなぐか（つなぐ時は nmtui の画面が出ます。下の「既知の事」）
3. 終わるとログインの画面になります。ログインした後、`fdtp` で FDTP、`startx` で XFCE が始まります。

最初の起動は、信頼できる Wi-Fi で行い、強いパスワードにしてください。終わったら、ケーブルにつないで、まず更新してください（下の「更新」）。

### 本体の内蔵メモリと純正の辞書（日本版 DM250 のみ）

最初の起動で利用者を作った後、本体の内蔵メモリの文書を **読み取り専用** で見られるようにし、純正の電子辞書と ATOK の固有名詞を Mozc に取り込みます（自動。何も聞きません）。日本版 DM250 である事を確かめてから行い、確かめられない時（ほかの機種・内蔵メモリが読めない等）は何もせず、ログインの時に案内を出します。

- 取り込んだ後の SD カードには、ATOK の固有名詞の写し（Mozc の辞書）ができます。**その SD カードの写しを人に渡さないでください。**
- 本体の内蔵メモリには書きません。

## ssh

**ssh は既定で閉じています。** 使う時は、ポメラの上で次を打ちます（sudo のパスワードを聞かれます）。

```
pomera-ssh on       # ssh を開き、つないでいるアドレスを出す
pomera-ssh off      # 閉じる
pomera-ssh status   # 今の状態
```

root での ssh のログインはできません（`PermitRootLogin no`）。

## 更新

**自動の更新はしません。** ログインの時に、入っていない安全の更新（Debian の security の更新）の件数を知らせます（数えるのは、最後に `apt update` した時の一覧からです。勝手に通信はしません）。更新は、ケーブルにつないで次を打ちます。

```
sudo apt update && sudo apt full-upgrade
```

- カーネルと U-Boot は、yasoteien/pomera-kernel の apt の置き場（https://yasoteien.github.io/pomera-kernel-apt/ 、署名つき）から、この操作で更新されます。新しいカーネルは次の起動で 1 回だけ試し、正常なら確定します（詳しくは https://github.com/yasoteien/pomera-kernel ）。
- 更新の途中で電池が切れた時は、次の起動で途中の設定を片付けます。片付かなかった時は、ログインの時に直し方を案内します。

## 既知の事

- **Wi-Fi は nmtui で設定します。** 項目の移動は **Tab キーと矢印キー**、決定は Enter です（画面にキーの説明は出ません）。あとから設定し直す時も `nmtui` を打ちます。
- **DM250 の Wi-Fi は 2.4GHz だけです。** 5GHz のアクセスポイントは一覧に出ません。
- Bluetooth は最初から電源が入っています。
- SD カードの中身と hibernate の写し（swap）は暗号化していません。SD カードをなくした時は、中の文書が読まれうると考えてください。
- 利用者は `input` の組に入っています（FDTP がキーを直接読むため）。同じ利用者で動くほかのプログラムも、キー入力を読める事になります。
- FDTP の LocalSend のブラウザでの受け取りは暗号化されていません（同じ Wi-Fi の中で使ってください）。
- このイメージには、ソースを公開していないバイナリ（FDTP の `fdtp-dict`、純正の辞書を読む物）が 1 つ入っています（NOTICE.md）。
- 大事な文書は、SD カードのほかにも控えを取ってください。

## ほかのイメージからの乗り換え

乗り換えの手順はここには書きません。Debian 11 の環境をなるべく残して Debian 13 へ移した時の記事を、参考にしてみてください。

- ポメラ DM250 の Debian 11 環境をなるべく残して Debian 13 へ移行した大まかな手順: https://note.com/yasoteien/n/n2ea462876585

## 中身の出どころとライセンス

[NOTICE.md](NOTICE.md) を見てください。変更の記録は [CHANGELOG.md](CHANGELOG.md) にあります。

英語の説明は [README.en.md](README.en.md) にあります（日本語が正です）。
