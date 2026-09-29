# v2rayN: routing rules for Russia, tuned for Xray TUN

[Русский](README.md) | **English**

Three routing rule sets for [v2rayN](https://github.com/2dust/v2rayN) in **Xray TUN** mode.
All of them use the `IPIfNonMatch` domain strategy. The rules rely on the
[runetfreedom](https://github.com/runetfreedom/russia-v2ray-rules-dat) geo files.
Ready-to-use files are in [Releases](https://github.com/OushenView/v2rayn-ru-xraytun/releases).

| set | via proxy | direct |
|---|---|---|
| Всё, кроме РФ (Whitelist) | everything outside Russia | Russian domains and IPs, local network, qBittorrent |
| Заблокированное (Blacklist) | resources blocked in Russia (`ru-blocked`), Discord, DNS 1.1.1.1 and 8.8.8.8 | everything else, qBittorrent |
| Всё (Global) | everything except the local network | local network |

QUIC (UDP/443) is blocked in every set, so browsers fall back to TCP, where Xray can see the site name.

In v2rayN, the name gets a version prefix: `V1-RU-Xtun-Всё, кроме РФ (Whitelist)`. `V1` is the
version of the sets and matches the release number.

## Requirements

- v2rayN with the Xray core (tested on 7.25.2).
- TUN enabled. In "Settings → Option Setting → Tun Mode settings", "Legacy TUN Protect" must be
  off, otherwise the TUN is provided by sing-box.
- Geo files and DNS from the "Russia" preset — step 1 below.

## Installation

> **Apply the "Russia" preset first.** The rules use the `ru-blocked` and `category-ru` categories
> from the runetfreedom geo files. The default v2rayN geo files don't have them, and the core
> won't start without them.

1. **Settings → Regional presets setting → Russia.** v2rayN downloads the runetfreedom geo files
   and DNS settings. It also adds runetfreedom's own rule sets; you can delete them.
2. **Settings → Option Setting → v2rayN settings → Routing rules source (optional).** Paste
   the link and save:
   ```
   https://github.com/OushenView/v2rayn-ru-xraytun/releases/latest/download/template.json
   ```
3. **Settings → Routing Setting → Import Rules.** Three sets appear:
   `V1-RU-Xtun-Всё, кроме РФ (Whitelist)`, `V1-RU-Xtun-Заблокированное (Blacklist)`
   and `V1-RU-Xtun-Всё (Global)`.
4. Pick a set in the main window or in the tray menu.

Import while the proxy is connected: v2rayN downloads the rules through it.

If the core fails to start with an error about `ru-blocked`, the geo files are still the default
ones. Repeat step 1 or update GeoFiles via "Check Update".

You can also add a single set without the template. In "Routing Setting", create a set, choose
`IPIfNonMatch` as the "Domain strategy", paste a link to a release file into "URL (optional)" and
click "Import Rules From Subscription URL". Links to the files of the latest release:

```
https://github.com/OushenView/v2rayn-ru-xraytun/releases/latest/download/whitelist.json
https://github.com/OushenView/v2rayn-ru-xraytun/releases/latest/download/blacklist.json
https://github.com/OushenView/v2rayn-ru-xraytun/releases/latest/download/global.json
```

## Updating

v2rayN doesn't update the sets on its own. New versions are published as
[releases](https://github.com/OushenView/v2rayn-ru-xraytun/releases). Two ways to update:

- Run "Import Rules" again. A new version comes with a new number in the name (`V2-RU-Xtun-…`);
  delete the old sets.
- One set at a time: open it, paste its release file link into "URL (optional)", click "Import
  Rules From Subscription URL" and answer "No" to "Do you want to append rules?" — the rules are
  replaced.

## Customizing

### Your programs

The `Direct MY process` (Whitelist and Blacklist) and `Proxy MY process` (Blacklist) rules contain
a placeholder, `example.exe`, next to the default programs. Replace it with your own programs:
open the set in "Routing Setting", then the rule, and enter the names in the
"Process (Linux/Windows)" field, one per line or comma-separated.

Names are case-sensitive: write them exactly as on the "Details" tab of Task Manager
(`Discord.exe`, not `discord.exe`).

Example: games and Steam going direct, in the `Direct MY process` rule of Whitelist:

```
VALORANT-Win64-Shipping.exe
RiotClientServices.exe
vgc.exe
dota2.exe
cs2.exe
steam.exe
steamservice.exe
steamwebhelper.exe
```

The first three are the Valorant game, the Riot client and the Vanguard anti-cheat; the last three
are the Steam client, its service and its built-in browser. In Blacklist, games go direct anyway.

### Torrents

The `bittorrent` rule only recognizes the plain handshake. Encrypted connections, DHT and UDP
trackers slip past it and go through the proxy in Whitelist. That's why qBittorrent is sent direct
by process name. If you use another client, add it to `Direct MY process`.

### Your own server outside the tunnel

Whitelist has a disabled `Direct MY IP` rule. Enter your server's IP and enable the rule if you
reach the server over SSH or through a nested client.

## Why separate rules for Xray TUN

In Xray TUN mode, v2rayN turns on `routeOnly` by itself. For every connection, Xray sees the real
destination IP and, separately, the site name from SNI.

- **`IPIfNonMatch`, not `IPOnDemand`.** With `IPIfNonMatch`, IP rules check the real address, with
  no DNS queries. With `IPOnDemand`, for every connection that has a name Xray first resolves
  that name and matches IP rules against the DNS answer instead of the real address. That adds
  delay to new connections (4 seconds when DNS doesn't answer) and makes IP rules miss where the
  SNI name doesn't match the address, for example Reality with a borrowed SNI.
- **The last rule matches by IP** (`0.0.0.0/0`, `::/0`), not by port. In TUN it matches at once,
  without DNS. Connections that only carry a name (system proxy, SOCKS in apps) go through
  a second pass: Xray resolves the name and checks the geoip rules.

## Credits

- [runetfreedom/russia-v2ray-rules-dat](https://github.com/runetfreedom/russia-v2ray-rules-dat) —
  geo files (`ru-blocked`, `category-ru`) and the "Russia" preset in v2rayN
- [v2fly/domain-list-community](https://github.com/v2fly/domain-list-community)
- [2dust/v2rayN](https://github.com/2dust/v2rayN), [XTLS/Xray-core](https://github.com/XTLS/Xray-core)

## License

[MIT](LICENSE)
