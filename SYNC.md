# Sync Runbook (Server Repo)

Use this runbook to keep `tdimitriou/rustdesk-server` current with `rustdesk/rustdesk-server`.

## Remotes (expected)

- `origin` -> `https://github.com/tdimitriou/rustdesk-server.git`
- `upstream` -> `https://github.com/rustdesk/rustdesk-server.git`

## Routine upstream sync

```bash
git checkout master
git fetch upstream
git merge upstream/master
git push origin master
```

If upstream uses `main`, replace branch names accordingly.

## Fork note: Change ID (`RegisterPk` over TCP)

Upstream OSS hbbs answered `NOT_SUPPORT` for `RegisterPk` on **TCP/WebSocket** while handling it on **UDP**. The Flutter/Rust client uses TCP for Change ID checks, so this fork routes **TCP `RegisterPk`** through the same logic as UDP (`handle_register_pk_message`). Deploy updated `hbbs` before expecting Change ID to succeed from clients.

## Production-safe upgrade checklist

Before replacing `hbbs`/`hbbr` in production:

1. Back up:
   - `db_v2.sqlite3`, `db_v2.sqlite3-wal`, `db_v2.sqlite3-shm`
   - `id_ed25519`, `id_ed25519.pub`
   - current binaries (`hbbs`, `hbbr`, optional `rustdesk-utils`)
2. Stop services:
   - `rustdesksignal.service`
   - `rustdeskrelay.service`
3. Replace binaries only (preserve DB + keys)
4. Restart and verify logs/status

## Local security hygiene

- Never commit exported production data/keys.
- Keep `rustdesk-server-export/` ignored in `.gitignore`.
