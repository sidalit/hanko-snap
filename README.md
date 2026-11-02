<div align="center">

<img src="snap/gui/hanko.png" alt="Hanko logo" width="128" />

# Hanko

**Passkey-first authentication and user management server**

[![hanko](https://snapcraft.io/hanko/badge.svg)](https://snapcraft.io/hanko)
[![Base: core26](https://img.shields.io/badge/Base-core26-2C8C3C?logo=canonical&logoColor=white)](https://snapcraft.io/core26)
![Confinement: strict](https://img.shields.io/badge/Confinement-strict-2C8C3C)
[![Maintainer: sidaliteir](https://img.shields.io/badge/Maintainer-sidaliteir-2C8C3C?logo=snapcraft&logoColor=white)](https://snapcraft.io/publisher/sidaliteir)
![Architectures: amd64 and arm64](https://img.shields.io/badge/Architectures-amd64%20%7C%20arm64-2C8C3C?logo=linux&logoColor=white)

[Getting Started](#getting-started) · [Configure](#configure) · [Limitations](#limitations) · [Build](#build) · [Support](#support) · [License](#license)

Unofficial snap packaging for the [Hanko](https://github.com/teamhanko/hanko) backend, an open-source alternative to Auth0 and Clerk with passkeys, passwords, passcodes, MFA and SSO.

</div>

---

## Getting Started

<a href="https://snapcraft.io/hanko"><img alt="Get it from the Snap Store" src="https://snapcraft.io/en/dark/install.svg" width="180"></a>

Hanko needs a PostgreSQL, MySQL, MariaDB or CockroachDB database; the snap does not include one. Create an empty database and user, then point the snap at it and start the service:

```bash
sudo snap install hanko
sudo snap set hanko database-url='postgres://hanko:secret@localhost:5432/hanko?sslmode=disable'
sudo snap start --enable hanko.daemon
```

The service applies database migrations, then serves the public API on `http://localhost:8000` and the admin API on `http://localhost:8001`. Check it with `curl http://localhost:8000/health/ready`. It is initially disabled; stop it with `sudo snap stop --disable hanko.daemon`.

The `hanko` command uses the same database and config file, for example `sudo hanko user import users.json` or `sudo hanko config show`.

## Configure

These options configure the background service and the `hanko` command; changes restart the service when active or enabled.

| Option | Accepted value | Default |
| --- | --- | --- |
| `database-url` | `postgres://`, `mysql://`, `mariadb://` or `cockroach://` URL | Unset (uses `database` in the config file) |
| `public-address` | `host:port`, `[IPv6]:port` or `:port` | `127.0.0.1:8000` |
| `admin-address` | `host:port`, `[IPv6]:port` or `:port` | `127.0.0.1:8001` |
| `auto-migrate` | `true` or `false` | `true` |

```bash
sudo snap set hanko public-address=0.0.0.0:8000
sudo snap get hanko
```

Everything else is in `/var/snap/hanko/common/config.yaml`, created at install time with a random `secret_keys` entry and readable by root only. Set the WebAuthn relying party and origins to your frontend's domain, the CORS origins, and SMTP for passcode emails, then run `sudo snap restart hanko.daemon`. See the [configuration reference](https://docs.hanko.io/references/config).

Snap options are readable by every local user. To keep the database password private, leave `database-url` unset and set `database` in the config file instead.

The admin API has no authentication. Keep `admin-address` on localhost or put it behind an access-controlled proxy. Serve the public API over HTTPS through a reverse proxy.

## Limitations

- An external database is required; SQLite and in-memory storage are not supported upstream.
- Back up `config.yaml` with the database: without its `secret_keys` the stored signing keys cannot be decrypted.
- The `hanko` command reads and writes import and export files in your home directory only.
- `hanko schema markdown` needs Node.js and is not supported.

## Build

```bash
git clone https://github.com/sidalit/hanko-snap.git
cd hanko-snap
sudo snap install snapcraft --classic
snapcraft pack
```

## Support

- **Installation or snap issues:** [open a packaging issue](https://github.com/sidalit/hanko-snap/issues).
- **Application bugs:** [report them upstream](https://github.com/teamhanko/hanko/issues).

## License

Snap packaging: [MIT](https://github.com/sidalit/hanko-snap/blob/main/LICENSE). Hanko is licensed under [AGPL-3.0](https://github.com/teamhanko/hanko/blob/main/LICENSE).
