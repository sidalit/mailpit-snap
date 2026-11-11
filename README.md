<div align="center">

<img src="snap/gui/mailpit.png" alt="Mailpit logo" width="128" />

# Mailpit

**Catch and inspect the email your applications send.**

[![mailpit](https://snapcraft.io/mailpit/badge.svg)](https://snapcraft.io/mailpit)
[![Base: core26](https://img.shields.io/badge/Base-core26-2C8C3C?logo=canonical&logoColor=white)](https://snapcraft.io/core26)
![Confinement: strict](https://img.shields.io/badge/Confinement-strict-2C8C3C)
[![Maintainer: sidaliteir](https://img.shields.io/badge/Maintainer-sidaliteir-2C8C3C?logo=snapcraft&logoColor=white)](https://snapcraft.io/publisher/sidaliteir)
![Architectures: amd64 and arm64](https://img.shields.io/badge/Architectures-amd64%20%7C%20arm64-2C8C3C?logo=linux&logoColor=white)

[Getting Started](#getting-started) · [Configure](#configure) · [Limitations](#limitations) · [Build](#build) · [Support](#support) · [License](#license)

Unofficial snap packaging for [Mailpit](https://github.com/axllent/mailpit), an email and SMTP testing tool with a web interface and an API for developers.

</div>

---

## Getting Started

<a href="https://snapcraft.io/mailpit"><img alt="Get it from the Snap Store" src="https://snapcraft.io/en/dark/install.svg" width="180"></a>

```bash
sudo snap install mailpit
printf 'To: you@example.com\nSubject: Test\n\nHello\n' | mailpit sendmail
```

Open [http://127.0.0.1:8025](http://127.0.0.1:8025) to read the message. The service starts automatically and listens on this machine only. Point your application at SMTP host `127.0.0.1`, port `1025`, without authentication or TLS.

Automated tests can read the mailbox through the API, for example `curl http://127.0.0.1:8025/api/v1/messages`, and wait for the service with `mailpit readyz --wait`.

## Configure

| Option | Accepted value | Default |
| --- | --- | --- |
| `smtp-address` | `host:port` or `[IPv6]:port`; port `1`–`65535` | `127.0.0.1:1025` |
| `http-address` | `host:port` or `[IPv6]:port`; port `1`–`65535` | `127.0.0.1:8025` |
| `pop3-address` | `host:port` or `[IPv6]:port`; port `1`–`65535` | `127.0.0.1:1110` |

```bash
sudo snap set mailpit smtp-address=0.0.0.0:1025 http-address=0.0.0.0:8025
sudo snap get mailpit
```

Changes restart an enabled or running service; `sudo snap unset mailpit http-address` restores a default. The `mailpit` and `mailpit.sendmail` commands use the same addresses. The POP3 server only starts once POP3 credentials are set.

All other [upstream settings](https://mailpit.axllent.org/docs/configuration/runtime-options/) go in `/var/snap/mailpit/common/mailpit.env`, one `MP_VARIABLE=value` per line in shell syntax. The file is created at install with commented examples and is readable by root only. Run `sudo snap restart mailpit.daemon` after editing it. The options above take precedence over `MP_SMTP_BIND_ADDR`, `MP_UI_BIND_ADDR` and `MP_POP3_BIND_ADDR` in this file.

The web interface and API have no authentication by default: anyone who can reach them can read and delete all mail. Before listening on other interfaces, create a password file and name it in `mailpit.env`:

```bash
printf 'admin:your-strong-password\n' | sudo tee /var/snap/mailpit/common/ui-auth > /dev/null
sudo chmod 600 /var/snap/mailpit/common/ui-auth
echo 'MP_UI_AUTH_FILE=/var/snap/mailpit/common/ui-auth' | sudo tee -a /var/snap/mailpit/common/mailpit.env
sudo snap restart mailpit.daemon
```

Password files, TLS certificates and keys, and relay, forward and tag configuration files must be under `/var/snap/mailpit/common`; the service cannot read other locations. Captured mail is stored in `/var/snap/mailpit/common/mailpit.db`, readable by root only. To export it, run `mailpit dump --http http://127.0.0.1:8025 ~/mailpit-export`.

`mailpit.sendmail` accepts the usual sendmail options and delivers the message it reads from standard input to the service. To also provide it as `sendmail`:

```bash
sudo snap alias mailpit.sendmail sendmail
```

## Limitations

- Updates come through snapd. The version check is disabled and `mailpit version --update` cannot replace the packaged executable.
- Snap commands need a login session. Programs running as a system user without one, such as PHP-FPM as `www-data`, cannot run `mailpit.sendmail`; let them send to the SMTP port instead.
- A `mailpit` server started by hand is a separate instance with a temporary mailbox. Stop the service first or give it other addresses with `--smtp` and `--listen`.
- `mailpit dump` and `mailpit ingest` only reach non-hidden files in your home directory.
- `mailpit readyz` does not read `mailpit.env`; add `--https` or `--webroot` when you enable TLS or set a web root there.
- The snap options take TCP addresses only; upstream's `unix:` socket addresses are not accepted.
- `snap revert` does not roll back the mailbox, which a newer release may have migrated.

## Build

```bash
git clone https://github.com/sidalit/mailpit-snap.git
cd mailpit-snap
sudo snap install snapcraft --classic
snapcraft pack
```

## Support

- **Installation or snap issues:** [open a packaging issue](https://github.com/sidalit/mailpit-snap/issues).
- **Application bugs:** [report them upstream](https://github.com/axllent/mailpit/issues).

## License

Snap packaging: [MIT](https://github.com/sidalit/mailpit-snap/blob/main/LICENSE).
