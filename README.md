<div align="center">

<img src="build/icon.svg" alt="" width="88" height="88" />

# Pishcord

**A fast, lightweight Discord client with Vencord built in, encrypted DNS, and optional connection profiles for hard-to-reach networks.**

[![Platforms](https://img.shields.io/badge/platforms-Windows%20%7C%20macOS%20%7C%20Linux-5865F2?style=flat-square)](#installation)
[![License](https://img.shields.io/badge/license-GPL--3.0--or--later-green?style=flat-square)](LICENSE)
[![Release](https://img.shields.io/github/v/release/Newipe/Pishcord?style=flat-square)](../../releases/latest)
[![Tests](https://img.shields.io/github/actions/workflow/status/Newipe/Pishcord/test.yml?style=flat-square&label=tests)](../../actions/workflows/test.yml)

</div>

---

Pishcord is a desktop Discord client built on [Vesktop](https://github.com/Vencord/Vesktop), with [Vencord](https://vencord.dev) already installed. On top of what Vesktop gives you, it adds two things for people on difficult networks: **DNS over HTTPS** and **Connection Profiles**.

A connection profile sends only the Discord traffic that a relay approves through the Newipe relay (or one you host yourself). That can get around DNS- and SNI-based filtering, including on some networks in Iran. It can't help if the relay's own IP or port is blocked, so how well it works depends on your network. More on that [below](#connection-profiles).

From Vesktop, you also get:

- Vencord preinstalled
- A much lighter, faster app than the official Discord client
- Linux screen sharing with sound, including Wayland
- Better privacy, since Discord has no access to your system
- Global keybinds, including push to talk (see [Global Keybinds](#global-keybinds))

<table>
  <tr>
    <td align="center">
      <img src="https://github.com/user-attachments/assets/42844cd0-35b9-4170-889d-0a5e3349bca9" alt="Pishcord First-Launch" width="300" />
    </td>
    <td align="center">
      <img src="https://github.com/user-attachments/assets/0a72581d-58bb-467f-a4b2-31026c26742f" alt="Pishcord Settings" width="600" />
    </td>
  </tr>
</table>

## Contents

- [Overview](#overview)
- [Installation](#installation)
- [Connection Profiles](#connection-profiles)
- [DNS over HTTPS](#dns-over-https)
- [Global Keybinds](#global-keybinds)
- [Recommended Resolvers](#recommended-resolvers)
- [Settings](#settings)
- [FAQ and Troubleshooting](#faq-and-troubleshooting)
- [Building from Source](#building-from-source)
- [Credits](#credits)
- [Support Pishcord](#support-pishcord)
- [License](#license)

---

## Overview

What Pishcord adds on top of Vesktop:

- **DNS over HTTPS.** Encrypts your DNS lookups using any RFC 8484-compatible resolver. Pick a built-in provider or paste your own URL (SNI-proxy resolvers work too).
- **Fallback control.** You decide whether Pishcord may drop back to ordinary DNS when the resolver can't be reached.
- **Resolver testing.** Check a hostname straight from the settings panel.
- **Connection Profiles.** Route approved Discord HTTPS and WebSocket traffic through an HTTPS relay, with optional TURN for voice. No VPN needed.

And what comes with the base client: Vencord's plugins, themes and tweaks, plus native Wayland support and screen sharing with audio on Linux.

> [!NOTE]
> Pishcord is a fork of [Vesktop](https://github.com/Vencord/Vesktop). The base client and its upstream work are credited [below](#credits). This fork adds the DoH integration and connection profiles.

---

## Installation

Grab a build from the **[Releases](../../releases/latest)** page.

| Platform | Packages                               |
| -------- | -------------------------------------- |
| Windows  | `.exe` installer, `.zip`               |
| macOS    | `.dmg`, `.zip`                         |
| Linux    | `.AppImage`, `.deb`, `.rpm`, `.tar.gz` |

> [!TIP]
> macOS builds are unsigned, so Gatekeeper may block the app. Right-click it and choose **Open**, or allow it under **System Settings → Privacy & Security**. If macOS says the app is damaged, run `xattr -cr /Applications/Pishcord.app`.

---

## Connection Profiles

A connection profile changes how **Discord** connects, and only Discord. Pishcord sends the HTTPS and WebSocket destinations the profile approves through an HTTPS CONNECT proxy. If the profile includes TURN settings, voice can use that relay too. Everything else, including other apps and any destination outside the profile's policy, keeps using your normal connection.

### Why it helps on restricted networks

Some networks, including some in Iran, tamper with DNS answers or filter TLS connections by reading the Server Name Indication (SNI). The built-in **Newipe** profiles are aimed at exactly that. Pishcord first opens a TLS connection to the relay, then sends the approved Discord requests through it. Your network can see the relay's address, but the CONNECT destination and Discord's own SNI travel inside the encrypted connection.

So it can help when blocking is based on DNS or on the Discord hostname in SNI. It won't help if the relay's IP address or port is blocked, and speed and availability will vary from one ISP to the next.

> [!WARNING]
> A profile is **not a VPN** and doesn't guarantee access on every network.
> Your network can still see the relay's IP address, connection timing and traffic volume. The relay operator can see your source IP, the destination host and port you asked for, timing and volume. The relay does not decrypt Discord's inner TLS traffic.

### Profiles

| Profile                            | What it is                                                                                       |
| ---------------------------------- | ------------------------------------------------------------------------------------------------ |
| **No Profile — Direct Connection** | A normal, direct Discord connection. DoH can be enabled.                                         |
| **Newipe**                         | Built-in relay profile for restricted networks, including Iran. Nothing to configure.            |
| **Newipe 2**                       | Backup relay. Use it when the main Newipe relay is unreachable.                                  |
| **Custom**                         | Your own compatible Pishcord-relay server. Needs a profile name and a Network Configuration URL. |

### Use a built-in profile

On first launch, pick **Newipe** or **Newipe 2** under **Profiles → Connection Profile**, then press **Get Started**. Later, switch profiles any time in **User Settings → Pishcord → Connection Profile**. Pishcord fetches and validates the network configuration for you.

### Add a custom profile

1. Set up a compatible [Pishcord-relay](https://github.com/Newipe/Pishcord-relay) server.
2. Copy the **Network Configuration URL** it gives you.
3. In Pishcord, open **User Settings → Pishcord → Connection Profile**.
4. Choose **Manage Profiles → Add Custom Profile**.
5. Enter a name and the URL, then save and select the profile.

That's all it needs. Pishcord checks the server's response before accepting the profile. You can edit or remove custom profiles under **Manage Profiles**; built-in ones are locked.

### Good to know

- **Profiles and DoH are mutually exclusive.** Selecting a profile turns DoH off, and it stays unavailable until you go back to Direct Connection. Even then it doesn't switch itself back on.
- **The relay is in charge.** Its Network Configuration endpoint supplies the proxy, routing policy, allowed ports and temporary TURN settings. Pishcord ships no production domain allowlist of its own.
- **Built-in profiles refresh at startup** from [`profiles.json`](profiles.json) in this repo. Restart Pishcord to pick up changes. If the download fails or looks invalid, the packaged profiles stay in use.
- **No guessing.** If the configuration endpoint is unreachable or returns bad data, Pishcord won't apply a partial or hardcoded fallback policy. A policy that was already validated stays active while Pishcord retries.
- **No silent direct connections.** If the proxy is down, approved destinations aren't quietly sent direct. Retry the profile, or pick **No Profile** to get normal networking back.
- **TURN is optional.** If it's unavailable, voice may fall back to direct ICE. The HTTPS proxy policy doesn't change.

---

## DNS over HTTPS

Normally your DNS queries travel in plaintext, so whoever runs the network's resolver can read, change or block them. DoH wraps those queries in HTTPS and sends them to a resolver you pick.

It protects the lookup and nothing more. It doesn't encrypt your Discord messages, and it doesn't replace Discord's own TLS. DoH is configured inside Chromium's host resolver, so it only affects Pishcord, not your other apps or your operating system.

> [!NOTE]
> DoH can't be turned on while a connection profile is selected. See [Good to know](#good-to-know).

### Turn it on

1. Open **User Settings** (the gear icon).
2. Choose **Pishcord** in the sidebar.
3. Under **DNS over HTTPS**, pick a provider, or choose **Custom** and enter an HTTPS resolver URL.
4. Press **Test Resolution** to check that the resolver can look up `discord.com`.

If you're setting up for the first time, choose **No Profile — Direct Connection**, set your resolver under **DNS over HTTPS → Secure DNS Provider**, then press **Get Started**.

### What the status means

- **Encrypted:** a valid DoH resolver is active.
- **Incomplete:** you're still editing a custom URL. The last valid resolver stays active until you finish.
- **Disabled:** Pishcord is using the system resolver.

**Allow Insecure Fallback** is off by default. Turn it on and Pishcord may fall back to ordinary DNS if it can't reach your DoH resolver.

New connections pick up resolver changes straight away. Existing ones may hold on to addresses they already resolved, so restart Pishcord if something looks stale.

---

## Global Keybinds

Set them up in Pishcord's settings, or in Discord's Keybinds and Voice & Video settings.

- **Windows and Linux (X11):** bind any keys or mouse buttons in Pishcord's keybind settings.
- **macOS:** same thing, but Pishcord needs the Accessibility permission.
- **Linux (Wayland):** Wayland doesn't let apps listen for global input. Turn on global shortcuts in Pishcord's keybind settings, then assign the keys in your system settings. You need a desktop that supports the GlobalShortcuts portal, like GNOME or KDE.

You can also trigger actions from other programs, such as your compositor's keybinds:

```sh
pishcord --run-shortcut toggleMute

# Append :down or :up to only press or release, e.g. for push to talk
pishcord --run-shortcut pushToTalk:down
pishcord --run-shortcut pushToTalk:up
```

For lower latency, write the same commands as lines to the socket at `$XDG_RUNTIME_DIR/pishcord.sock`:

```sh
echo "run-shortcut pushToTalk:down" | socat - UNIX-CONNECT:$XDG_RUNTIME_DIR/pishcord.sock
```

Run `pishcord --help` to see every action.

---

## Recommended Resolvers

Any RFC 8484-compatible resolver works. These are the usual suspects:

| Provider       | URL                                    | Notes                           |
| -------------- | -------------------------------------- | ------------------------------- |
| **Cloudflare** | `https://cloudflare-dns.com/dns-query` | General-purpose                 |
| **Google**     | `https://dns.google/dns-query`         | Global coverage                 |
| **Quad9**      | `https://dns.quad9.net/dns-query`      | Malware and phishing protection |
| **OpenDNS**    | `https://doh.opendns.com/dns-query`    | Optional content filtering      |
| **Newipe**     | `https://newipe.qd.je/dns-query`       | Iran-focused SNI-proxy resolver |
| **VanillaApp** | `https://dns.vanillapp.ir/dns-query`   | Requires IP registration        |

Resolvers built for restricted networks may be slower, or not work at all, outside of them.

The built-in list is loaded from [`resolvers.json`](resolvers.json) at startup. If that can't be fetched, Pishcord uses the list packaged with the app.

---

## Settings

Pishcord keeps its settings in `settings.json` in the app's data directory:

| Platform | Path                                                   |
| -------- | ------------------------------------------------------ |
| Windows  | `%appdata%\Pishcord\settings.json`                     |
| Linux    | `~/.config/Pishcord/settings.json`                     |
| macOS    | `~/Library/Application Support/Pishcord/settings.json` |

If you can't get to the settings window (say, Discord won't load), quit Pishcord and edit the file by hand.

| Key                | Type      | Default | Description                                                  |
| ------------------ | --------- | ------- | ------------------------------------------------------------ |
| `enableDoh`        | `boolean` | `false` | Turn DoH on. Ignored while a profile is selected.            |
| `dohUrl`           | `string`  | —       | The DoH resolver URL.                                        |
| `dohAllowFallback` | `boolean` | `false` | Allow ordinary DNS if DoH is unavailable.                    |
| `profileId`        | `string`  | —       | The selected profile's ID. Omit it or use `null` for Direct. |

For example, DoH with Quad9 and no profile:

```json
{
    "enableDoh": true,
    "dohUrl": "https://dns.quad9.net/dns-query",
    "dohAllowFallback": false,
    "profileId": null
}
```

A selected profile switches DoH off no matter what `enableDoh` says, so set `profileId` to `null` (or choose **No Profile — Direct Connection**) before enabling it.

Built-in profiles live in [`profiles.json`](profiles.json). Custom profiles are stored separately in `custom-profiles.json`. Temporary TURN credentials are only ever kept in memory.

---

## FAQ and Troubleshooting

**Does DoH encrypt my Discord messages or calls?**  
No. DoH only encrypts DNS lookups. Discord's own TLS connections protect your messages and other app traffic.

**Will Pishcord work where Discord is blocked, like in Iran?**  
It can help. The Newipe profiles are built for DNS interference and SNI filtering, and DoH can help with some DNS-based blocking. Neither one is a guarantee: if your network blocks the relay by IP or port, or uses a method these can't beat, they won't get you through.

**Is a connection profile a VPN?**  
No. It only routes the Discord HTTPS and WebSocket destinations the profile allows, through the relay. Voice can use TURN when the profile provides it. Everything else keeps using your normal connection.

**Can I use my own DNS resolver or relay?**  
Yes. Use any standard DoH endpoint for DNS, or add a compatible Pishcord-relay server as a custom profile.

**Can I share a custom profile with someone?**  
Share the profile name and its Network Configuration URL. Don't copy temporary TURN credentials, since they're short-lived and personal to you.

### Something's not working

| Problem                               | What to try                                                                                               |
| ------------------------------------- | --------------------------------------------------------------------------------------------------------- |
| Custom resolver shows **Incomplete**  | Enter a valid `https://` URL, then press Enter or choose a provider.                                      |
| **Test Resolution** fails             | Make sure the resolver is reachable, then try a different provider.                                       |
| Discord won't load after enabling DoH | Try another resolver. Only turn on **Allow Insecure Fallback** if you're okay with ordinary DNS.          |
| Discord is stuck connecting           | The address it got back may be blocked on your network. Try a resolver or profile built for your network. |
| Profile shows **Unavailable**         | Check the relay and its Network Configuration endpoint, click **Retry**, or select **No Profile**.        |
| Profile shows **Invalid config**      | Check the configuration and temporary credentials the relay is returning.                                 |
| Discord won't load with a profile     | Retry once the relay is reachable, or switch to **No Profile** in settings.                               |
| macOS says the app is damaged         | Follow the Gatekeeper steps in [Installation](#installation).                                             |

---

## Building from Source

You'll need [Git](https://git-scm.com), [Node.js](https://nodejs.org) 22 or newer, and [pnpm](https://pnpm.io) 11 or newer.

```sh
git clone https://github.com/Newipe/Pishcord
cd Pishcord
pnpm install
pnpm start          # Build and run
pnpm start:watch    # Rebuild on file changes
pnpm test           # Lint and typecheck
pnpm lint:fix       # Auto-fix lint issues
```

To package the app, run `pnpm package`, or `pnpm package:dir` for an unpacked directory. For a single target, use `pnpm electron-builder --linux deb` and swap in whichever target you need.

Releases are built by [`.github/workflows/release.yml`](.github/workflows/release.yml) whenever a `v*` tag is pushed.

> [!NOTE]
> On Linux, builds use LibVesktop for D-Bus events. Prebuilt binaries are used by default, but building it yourself needs native dependencies:
>
> - **Debian/Ubuntu:** `build-essential python3 curl pkg-config libglib2.0-dev`
> - **Fedora:** `@c-development @development-tools python3 curl pkgconf-pkg-config glib2-devel`

---

## Credits

Pishcord stands on a lot of other people's work:

- **[Vesktop](https://github.com/Vencord/Vesktop)** — the base client and Linux work.
- **[Vencord](https://vencord.dev)** — plugins, themes and tweaks.
- **[Discord RPC](https://github.com/Vencord/Vesktop/tree/main/packages/discord-rpc)** — Rich Presence support.
- **[venmic](https://github.com/Vencord/venmic)** — Linux screen-sharing audio capture.
- **[Newipe](https://github.com/Newipe)** — this fork, the DoH integration, the SNI-proxy resolver and the built-in connection profiles.

## Support Pishcord

If Pishcord is useful to you and you'd like to help keep it going, you can donate with crypto:

| Network   | Address                                      |
| --------- | -------------------------------------------- |
| **BEP20** | `0xdF83aB1a352018e3b5e2baBe19385C9BB54c0D11` |
| **TRC20** | `TXRYotwP995tcW6H91JtZCgiCjzyYdqxau`         |

> [!IMPORTANT]
> Double-check the network, and only send an asset the receiving address supports. Transfers on the wrong network or with an incompatible asset can be lost for good.

## License

GPL-3.0-or-later, inherited from upstream Vesktop. See [LICENSE](LICENSE) for details.

---

<div align="center">

Not affiliated with, endorsed by, or sponsored by Discord Inc.

</div>
