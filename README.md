# Guide: OpenVPN (Proton VPN) + LINE on Debian with GNOME

🌐 **English** | [Español](README.es.md)

Step-by-step guide to using **LINE** on **Debian (GNOME)** when there are location-based restrictions,
using **OpenVPN** with **Proton VPN**'s free plan and the LINE extension in a Chromium-based browser
(Brave, Chrome or Chromium).

> Tested on Debian 13 with GNOME, using a wired connection (RJ45).

---

## Contents

1. [Install OpenVPN](#1-install-openvpn)
2. [Create a Proton VPN account and download the .ovpn file](#2-create-a-proton-vpn-account-and-download-the-ovpn-file)
3. [Get the OpenVPN username and password](#3-get-the-openvpn-username-and-password)
4. [Import the VPN into GNOME](#4-import-the-vpn-into-gnome)
5. [Set the username and password on the VPN](#5-set-the-username-and-password-on-the-vpn)
6. [Connect and verify](#6-connect-and-verify)
7. [Use LINE in the browser](#7-use-line-in-the-browser)
8. [LINE with a dark theme](#8-line-with-a-dark-theme)
9. [Common problems](#9-common-problems)
10. [Security](#10-security)

---

## 1. Install OpenVPN

Open a terminal and run the commands below. They ask for your Debian user password; the characters stay hidden while you type, which is normal.

```bash
sudo apt update && sudo apt install -y openvpn network-manager-openvpn network-manager-openvpn-gnome
```

```bash
sudo systemctl restart NetworkManager
```

Installed packages:

| Package | What it does |
|---|---|
| `openvpn` | The VPN program |
| `network-manager-openvpn` | Lets NetworkManager use OpenVPN |
| `network-manager-openvpn-gnome` | Adds OpenVPN to GNOME's network settings |

> **Is Wi-Fi required?** No. The VPN works over any internet connection, including a wired one (RJ45).

---

## 2. Create a Proton VPN account and download the .ovpn file

OpenVPN is only the program; you also need a server. Proton VPN has a free plan.

1. Go to **protonvpn.com** → **Get Proton VPN** → choose the **Free** plan and sign up.
2. Go to **account.protonvpn.com**.
3. In the left menu: **Downloads**.
4. Scroll down to **OpenVPN configuration files** and choose:
   - **Platform:** Linux
   - **Protocol:** UDP
   - **Free server configs**
5. Pick a country and click **Download**.
   - **Japan (JP):** a good option for LINE, which is widely used there, but it is farther away, so it is somewhat slower.
   - **United States (US):** closer to Chile, so it is faster.
   - Downloading both and trying each is recommended.

The file is named something like `jp-free-16.protonvpn.udp.ovpn`.

> ⚠️ The free plan **has no servers in Chile**. If you need to appear in another Chilean city,
> you need a paid plan (Proton VPN Plus or another provider with servers in Chile).

---

## 3. Get the OpenVPN username and password

These are **not** your Proton email or password.

1. On **account.protonvpn.com** → left menu → **Account**.
2. Find the **OpenVPN / IKEv2 username** section.
3. It shows:
   - **OpenVPN username**, with a copy button next to it.
   - **OpenVPN password**, with a copy button; the eye icon only shows or hides it.

**Reset credentials** generates a **new** username and password, and the old ones stop working.
Use it if the connection fails with an authentication error or if someone else saw your credentials.

---

## 4. Import the VPN into GNOME

With the `.ovpn` file saved (in `~/Documents` in this example):

```bash
nmcli connection import type openvpn file ~/Documents/jp-free-16.protonvpn.udp.ovpn
```

Optionally, give it a clearer name:

```bash
nmcli connection modify jp-free-16.protonvpn.udp connection.id "ProtonVPN Japon"
```

You can also do this graphically: **Settings → Network → VPN (+) → Import from file…**

---

## 5. Set the username and password on the VPN

If the **"Authentication required"** window keeps popping up, the **username** is missing.

1. **Settings → Network →** gear icon ⚙️ next to **ProtonVPN Japon**.
2. **Identity** tab, **Authentication** section:
   - **Type:** `Password`
   - **User name:** paste the **OpenVPN username**.
   - **Password:** click the icon on the right of the field → **Store the password only for this user**, then paste the **OpenVPN password**.
3. Do not change **Gateway** or **CA certificate**.
4. Click **Apply**.

---

## 6. Connect and verify

1. Click the **top-right corner** of the screen, where the network, volume and battery icons are.
2. **VPN → ProtonVPN Japon** → turn it on.
3. A VPN or lock icon appears in the top bar.
4. Check on **whatismyipaddress.com** that the country shows **Japan**.

To disconnect, use the same menu and turn the VPN off.

From the terminal:

```bash
nmcli connection up "ProtonVPN Japon"
```

```bash
nmcli connection down "ProtonVPN Japon"
```

---

## 7. Use LINE in the browser

LINE has no official Linux app, but it does have an **official extension** for Chromium-based
browsers (**Brave**, Chrome, Chromium). It does not work in Firefox.

### 7.1 Prepare your phone

In the LINE app: **Home → Settings ⚙️ → Account**:

- Turn on **Allow login**.
- (Optional) Register an **email address** so you can log in with email and password.

### 7.2 Install the extension

1. Open **Brave** (or Chrome) → **chromewebstore.google.com**.
2. Search for **LINE** and make sure the developer is **LINE Corporation / LY Corporation**, because there are fake copies.
3. Click **Add to Brave / Add to Chrome**.

### 7.3 Log in

1. Click the LINE icon (it may be inside the extensions icon 🧩).
2. Log in with a **QR code** (scan it from your phone: Home → QR scanner) or with **email and password**.
3. Enter the **verification code** shown on the computer into your phone.
4. Your contacts and chats now appear.

> **Note:** *LINE Developers* (developers.line.biz) is for programmers who build bots and apps.
> It is **not** for chatting with your contacts.

Recommended order: **connect the VPN first, then open LINE**.

---

## 8. LINE with a dark theme

The LINE extension has no dark theme option. The solution is a **separate Brave window just for LINE**
with forced dark mode, so **only LINE** goes dark and your normal Brave is unchanged.

> Brave's global flag `brave://flags/#enable-force-dark` would darken **every** website, which is why
> a separate profile is used here. Extensions such as Dark Reader cannot change another extension's pages.

The scripts are in the [`scripts/`](scripts) folder. They assume Brave installed as a **Flatpak**
(`com.brave.Browser`) and the official LINE extension (ID `ophjlpahpchlmihnnnihgmmeilfjmjjc`).

| File | What it does |
|---|---|
| [`line-oscuro`](scripts/line-oscuro) | Opens LINE in its own window, in dark mode, using a separate Brave profile |
| [`line-login-claro`](scripts/line-login-claro) | Opens the same profile **without** dark mode, only to log in with the QR code |
| [`line-oscuro.desktop`](scripts/line-oscuro.desktop) | Shortcut "LINE (oscuro)" for the GNOME app menu |

### 8.1 Install

From the repository folder:

```bash
mkdir -p ~/.local/bin ~/.local/share/applications ~/.local/share/icons
```

```bash
cp scripts/line-oscuro scripts/line-login-claro ~/.local/bin/ && chmod +x ~/.local/bin/line-oscuro ~/.local/bin/line-login-claro
```

```bash
cp scripts/line-oscuro.desktop ~/.local/share/applications/
```

Optional icon (copied from the installed LINE extension in your normal Brave):

```bash
cp ~/.var/app/com.brave.Browser/config/BraveSoftware/Brave-Browser/Default/Extensions/ophjlpahpchlmihnnnihgmmeilfjmjjc/*/line_logo_128x128_on.png ~/.local/share/icons/line-oscuro.png
```

### 8.2 First use

1. Press **Super**, type **LINE** and open **LINE (oscuro)**.
2. The first time, the Chrome Web Store opens on the LINE page → **Add to Brave**. Then close that window.
3. **Log in in light mode.** In dark mode the QR code's green logo turns white and the phone may not
   recognize it. So log in once in light mode:

   ```bash
   ~/.local/bin/line-login-claro
   ```

   Scan the QR code **from inside the LINE app** (Home → QR icon in the search box), confirm on your phone
   and enter the 6-digit code.
4. Once you see your chats, **close that window**.
5. Open **LINE (oscuro)** again. The session is saved, so it goes straight to your chats in dark mode.

Tip: right-click its icon in the dock → **Pin to Dash / Add to Favorites**.

### 8.3 Notes

- LINE allows **only one computer session at a time**. If you use LINE (oscuro), log out of (or remove) the
  LINE extension in your normal Brave to avoid conflicts.
- If LINE ever asks you to log in again, repeat step 3.
- How it works: `line-oscuro` runs Brave with
  `--user-data-dir=<separate profile>` and
  `--enable-features=WebContentsForceDark:inversion_method/cielab_based/image_behavior/none`
  (darkens backgrounds and text without changing images), and opens the extension with `--app=` as its own window.
- To uninstall: delete the three files you copied and the profile folder
  `~/.var/app/com.brave.Browser/config/BraveSoftware/Brave-LINE-Oscuro`.

---

## 9. Common problems

| Problem | Solution |
|---|---|
| The password window keeps appearing | The *User name* is missing on the VPN (see step 5). |
| Authentication error | Make sure you are using the **OpenVPN** username and password, not your Proton account credentials. Check that no spaces were copied. If it still fails, use **Reset credentials** and enter the new ones. |
| The VPN option does not appear in GNOME | Install `network-manager-openvpn-gnome` and restart NetworkManager (step 1). |
| Slow connection | Try a closer server (US). |
| LINE asks for permission to log in | Turn on *Allow login* on your phone (step 7.1). |
| The QR code does nothing in LINE (oscuro) | Log in with `line-login-claro` (step 8.2), log out of LINE in your normal Brave, and disconnect the VPN while logging in. |

---

## 10. Security

- **Never** share passwords in chats, screenshots or repositories.
- If your OpenVPN credentials were exposed anywhere, use **Reset credentials** in Proton.
- Do not upload `.ovpn` files containing credentials to GitHub.
- Only install the LINE extension if the developer is the official one.
