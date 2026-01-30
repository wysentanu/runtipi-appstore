# Runtipi Appstore

> [!IMPORTANT]
> This Runtipi Appstore repository is intended for Orange Pi 3B v2.1
> running Armbian Linux v6.1 Minimal/IOT Debian 13 (Trixie).
> Compatibility with other hardware is untested and not guaranteed.

> [!WARNING]
> This is a private repository tailored for my personal use.
> You are free to use it in your own environment, but I cannot provide support,
> and I am not responsible for any data loss or issues resulting from updates to any applications in this repository.

This repository is my personal, opinionated version of the official Runtipi appstore,
containing various applications configured to suit my own homelab.

## Available Apps

| Name                                                                        | Logo                                                                    | Description                                                               | Note                                                                  |
|-----------------------------------------------------------------------------|-------------------------------------------------------------------------|---------------------------------------------------------------------------|-----------------------------------------------------------------------|
| [Brave](https://hub.docker.com/r/kasmweb/brave)                              | <img src="apps/brave/metadata/logo.jpg" alt="Brave" width="48" />       | Secure streamed Brave browser                                             |                                                                       |
| [Freqtrade](https://github.com/freqtrade/freqtrade)                         | <img src="apps/freqtrade/metadata/logo.jpg" alt="Freqtrade" width="48" /> | Free, open source crypto trading bot                                      |                                                                       |
| [Immich](https://github.com/immich-app/immich)                               | <img src="apps/immich/metadata/logo.jpg" alt="Immich" width="48" />     | Photo and video backup solution directly from your mobile phone           | Configured to leverage the RK3566 NPU for machine learning workloads. |
| [Linkding](https://github.com/sissbruecker/linkding)                        | <img src="apps/linkding/metadata/logo.jpg" alt="Linkding" width="48" /> | Self-hosted bookmark manager designed to be minimal, fast, and easy       |                                                                       |
| [Navicord](https://github.com/logixism/navicord)                            | <img src="apps/navicord/metadata/logo.jpg" alt="Navicord" width="48" /> | Headless Discord Rich Presence client for Navidrome                       | No GUI (Discord daemon).                                             |
| [Tailscale](https://github.com/tailscale/tailscale)                         | <img src="apps/tailscale/metadata/logo.jpg" alt="Tailscale" width="48" /> | Zero-config VPN using WireGuard and 2FA                                   | No GUI (CLI-managed).                                                 |

## Apps Policy

Because this is a privately curated repository, I do not accept feature requests or app addition requests.
If you wish to make modifications, feel free to fork the repository and maintain your own version.
Please note that applications may be removed at any time without notice.
