# pomera-dm250-image (Debian 13 SD card image for the Pomera DM250)

An SD card image that runs Debian 13 (trixie) on the King Jim Pomera DM250 (Japanese model). Write it to an SD card, insert the card, and power on.

The Japanese README ([README.md](README.md)) is the authoritative version.

- Contents: Debian 13 (trixie, armhf), a self-built Linux kernel 6.18.65 (Linux 6.18 LTS plus DM250 patches) and U-Boot, Japanese display and input (ja_JP.UTF-8, Japanese fonts, Mozc, the DM250 JIS keyboard layout), XFCE, and FDTP 1.18 (a writing tool, with the recommended settings applied).
- **For the Japanese DM250 only.** Do not use it on the DM250US or the DM200.
- Nothing is written to the internal memory (eMMC). Remove the SD card and the DM250 boots its stock software.
- This is a test release. Read the disclaimer below first.

Version: 2026.11.28 (Release: https://github.com/yasoteien/pomera-dm250-image/releases/tag/v2026.11.28 )

## Disclaimer

- This is a test release, provided without any warranty of function, quality, or fitness for a purpose. Use it at your own risk.
- I (yasoteien) am not liable for any problem or loss to your Pomera, SD card, or data caused by using it.
- This is unofficial and has no relation to King Jim, nor to the DM250 Debian 13 image published by ichinomoto on ekesete.net (https://www.ekesete.net/log/?p=9565). Please do not contact them about it.

## What you need

- **A microSD card of 8 GB or larger** (a 4 GB card is too small). Writing the image erases the whole card.
- A PC that can write SD cards (Windows, macOS, or Linux).
- `pomera-dm250-trixie-2026.11.28.img.xz` and `SHA256SUMS` from the Release.

## Verify the download (sha256)

`SHA256SUMS` has two lines: the `.img.xz` you download and the `.img` inside it. Make sure the hash of your `.img.xz` matches the `.img.xz` line.

- Windows (PowerShell): `Get-FileHash .\pomera-dm250-trixie-2026.11.28.img.xz -Algorithm SHA256`
- macOS: `shasum -a 256 -c --ignore-missing SHA256SUMS`
- Linux: `sha256sum -c --ignore-missing SHA256SUMS`

(`--ignore-missing` skips the `.img` line, since you do not have that file.) The values for this version:

```
c7cfb85764deeecfae43c2039757dc76e56dde5256812e8767884fd3ec4ef584  pomera-dm250-trixie-2026.11.28.img.xz
9a3e41db4d5ebce94af654052b2e1f955d1ee74d1c3a9cdf762226e35a27ee45  pomera-dm250-trixie-2026.11.28.img
```

## Writing the SD card

### Easiest (Windows, macOS, Linux): Raspberry Pi Imager

Raspberry Pi Imager (https://www.raspberrypi.com/software/) writes the `.img.xz` directly, without unpacking it.

1. Install and open Raspberry Pi Imager.
2. You do not need to choose a device ("No filtering" is fine).
3. Under "Choose OS", pick **"Use custom"** at the bottom of the list and select `pomera-dm250-trixie-2026.11.28.img.xz`.
4. Under "Choose storage", pick the SD card. **Check the size and name so that you do not pick another disk.**
5. If asked about OS customisation, answer **"No"** (this image asks for a user name and password on its first boot).
6. When writing and verifying are done, eject the card.

### Linux with dd

```
lsblk                          # find the SD card (e.g. /dev/sdX) by its size
xz -dc pomera-dm250-trixie-2026.11.28.img.xz | sudo dd of=/dev/sdX bs=4M conv=fsync status=progress
```

### macOS with dd

Install `xz` with Homebrew or similar (`brew install xz`).

```
diskutil list                  # find the SD card (e.g. /dev/diskN) by its size
diskutil unmountDisk /dev/diskN
xz -dc pomera-dm250-trixie-2026.11.28.img.xz | sudo dd of=/dev/rdiskN bs=4m conv=fsync
diskutil eject /dev/diskN
```

**Warning: if `of=` points to the wrong disk, that disk (even your PC's internal disk) is wiped.** Never type `sdX` or `diskN` literally; replace them with your own SD card. If in doubt, use Raspberry Pi Imager.

## First boot

Insert the card into the DM250 and power on.

1. It boots with its own U-Boot. On the first boot **`/` grows to fill the SD card and a 1 GiB swap partition is made at the end** (only for the hibernation image; the everyday swap is a 384 MB compressed-memory zram). This can take some tens of seconds.
2. A Japanese screen asks for:
   - a user name and password (**there is no shared default password**; only the one you set here works),
   - whether to connect to Wi-Fi (nmtui opens; see "Known issues").
3. Then the login prompt appears. After logging in, `fdtp` starts FDTP and `startx` starts XFCE.

Do the first boot on a Wi-Fi network you trust and choose a strong password. Afterwards, connect the cable and update first (see "Updates").

### Internal memory and the built-in dictionary (Japanese DM250 only)

After the user is made on the first boot, the documents in the internal memory are mounted **read-only**, and the built-in dictionary and ATOK proper nouns are imported into Mozc (automatically, without asking). This is done only after confirming that the machine is a Japanese DM250; otherwise nothing is done and a notice appears at login.

- After the import, the SD card holds a copy of the ATOK proper nouns (in the Mozc dictionary). **Do not give copies of that SD card to others.**
- Nothing is written to the internal memory.

## ssh

**ssh is off by default.** To use it, run on the Pomera (sudo asks for your password):

```
pomera-ssh on       # turn ssh on and show the address
pomera-ssh off      # turn it off
pomera-ssh status   # show the state
```

Logging in as root over ssh is not allowed (`PermitRootLogin no`).

## Updates

**There are no automatic updates.** At login, the number of pending Debian security updates is shown (counted from the list of your last `apt update`; nothing is fetched automatically). To update, connect the cable and run:

```
sudo apt update && sudo apt full-upgrade
```

- The kernel and U-Boot are updated by this from the signed apt repository of yasoteien/pomera-kernel (https://yasoteien.github.io/pomera-kernel-apt/). A new kernel is tried once on the next boot and kept if it boots normally (see https://github.com/yasoteien/pomera-kernel).
- If the battery runs out during an update, the next boot finishes the pending package setup. If that fails, a notice at login shows how to fix it.

## Known issues

- **Wi-Fi is set up with nmtui.** Move between items with **Tab and the arrow keys**, and press Enter to choose (the screen does not show these keys). Run `nmtui` again to change the setting later.
- **The DM250 Wi-Fi supports 2.4 GHz only.** 5 GHz access points do not appear.
- Bluetooth is powered on from the start.
- **The browser is NetSurf (a light browser).** Firefox is not included. If you install it (`sudo apt install firefox-esr`), one or two pages open, but it is slow (about 50 seconds per page; three pages take about 610 MB) and the 1 GB of memory runs short easily.
- The SD card and the hibernation image (swap) are not encrypted. If you lose the card, assume its documents can be read.
- The user is in the `input` group (FDTP reads the keyboard directly), so other programs running as the same user can also read key input.
- The LocalSend browser receiving in FDTP is not encrypted (use it only on your own Wi-Fi).
- The image contains one binary whose source is not published (`fdtp-dict` of FDTP, which reads the built-in dictionary); see NOTICE.md.
- Keep a backup of important documents outside the SD card.

## If you are starting out, or using another image

- **If you are starting out, start from this image.** This image is the only setup that is tested.
- If you use ichinomoto's image with pomera-kernel or FDTP installed via apt, apt updates keep arriving as before, but that combination is not tested. Moving to this image is recommended (back up your files in your home directory first).
- No migration steps are given here. As a reference, see this (Japanese) article about moving a Debian 11 setup to Debian 13 while keeping as much as possible:

- https://note.com/yasoteien/n/n2ea462876585

## Sources and licenses

See [NOTICE.md](NOTICE.md). The change log is [CHANGELOG.md](CHANGELOG.md).
