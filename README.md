# Lab-Umgebung

Eine kleine Spielwiese mit Docker: Nextcloud, Portainer und ein Mail-Fänger hinter einem
nginx, der nach Subdomain verteilt.

```
                        ┌──▶ nextcloud.localhost ──▶ Nextcloud 35 ──▶ MariaDB
 Browser ──▶ nginx :80 ─┼──▶ portainer.localhost ──▶ Portainer (Docker verwalten)
                        └──▶ mail.localhost      ──▶ Mailpit (fängt alle Mails ab)
```

## Starten

```bash
cp .env.example .env        # zwei Passwörter eintragen
docker compose up -d
```

Dann im Browser:

| Adresse | Dienst |
|---|---|
| http://nextcloud.localhost | Nextcloud (beim ersten Aufruf Admin-Konto anlegen) |
| http://portainer.localhost | Portainer (innerhalb von 5 Minuten Admin-Passwort setzen, sonst sperrt es sich) |
| http://mail.localhost | Mailpit: alle Mails, die Nextcloud verschickt |

`*.localhost` zeigt in allen modernen Browsern automatisch auf den eigenen Rechner, ein
Eintrag in `/etc/hosts` ist nicht nötig.

### Über einen Cloudflare-Tunnel

Weil nginx nur auf den **Anfang** des Hostnamens schaut (`nextcloud.*`), funktionieren auch
`nextcloud.deine-domain.tld` usw. Im Tunnel einfach jede Subdomain auf `http://localhost:80`
zeigen lassen.

> [!WARNING]
> Portainer hat Zugriff auf den Docker-Socket, also praktisch Root auf dem Host.
> Nur mit Login (am besten zusätzlich Cloudflare Access) nach außen geben.

## Änderungen (Oktober 2026)

- Passwörter aus der Compose-Datei in `.env` verschoben (Vorlage `.env.example`, `.env` ignoriert).
- MailHog (seit Jahren ohne Updates) durch **Mailpit** ersetzt; Nextcloud verschickt seine Mails direkt dorthin.
- Versionen statt `latest` (`nextcloud:35-apache`, `mariadb:lts`, `portainer-ce:lts`, `mailpit:v1`).
- WebSockets für Portainer-Konsole und Mailpit durchgereicht; Port 80 nur auf `127.0.0.1`.
