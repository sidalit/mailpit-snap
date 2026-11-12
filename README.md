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

The service starts automatically and listens on this machine only.
Open [http://127.0.0.1:8025](http://127.0.0.1:8025) to read the message, and point your application at SMTP host `127.0.0.1`, port `1025`.

## Configure

| Option | Accepted value | Default |
| --- | --- | --- |
| `smtp-address` | `host:port` or `[IPv6]:port` | `127.0.0.1:1025` |
| `http-address` | `host:port` or `[IPv6]:port` | `127.0.0.1:8025` |
| `pop3-address` | `host:port` or `[IPv6]:port` | `127.0.0.1:1110` |

```bash
sudo snap set mailpit http-address=0.0.0.0:8025
```

Changes restart the service, and `sudo snap unset mailpit <option>` restores a default. 
The `mailpit` and `mailpit.sendmail` commands follow the same addresses.
The web interface has no password by default, so protect it before listening on other interfaces.

To provide `mailpit.sendmail` as `sendmail`:

```bash
sudo snap alias mailpit.sendmail sendmail
```

## Limitations

- Programs running as a system user without a login session, such as PHP-FPM as `www-data`, cannot run `mailpit.sendmail`; let them send to the SMTP port instead.
- A `mailpit` server started by hand is a separate instance; stop the service first or give it other addresses.
- `mailpit dump` and `mailpit ingest` only reach non-hidden folders in your home directory.

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
