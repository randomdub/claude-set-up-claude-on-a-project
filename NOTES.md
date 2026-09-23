# Notes: setting up Claude Code on this project

## What's in CLAUDE.md, and what I left out

I kept `CLAUDE.md` to four short sections: a one-line description, the commands I actually run (dev, test, a single test, lint), the conventions, and a few lines of architecture. The conventions are rules Claude could get wrong without being told: use CommonJS, not ESM; get data only through `db/store.js`; return errors as `{ error }` JSON; use `node:test` + `supertest`, not Jest. The architecture section covers things you only see by reading several files, such as why `server.js` only calls `listen()` when run directly, and that the in-memory store is shared across tests.

I left out the course and submission steps from the README, because they're one-off tasks, not guidance for day-to-day work. I also left out a file-by-file listing, since Claude can find that itself, and generic advice like "write good tests". I didn't include any real config values beyond the fact that `PORT` exists.

## Permission rules

- **Allow:** `npm test`, `npm run lint`, `node --test`. They're safe, read-only checks that I want Claude to run freely after every change.
- **Ask:** `git push`, because it publishes my work, and `npm install`, because it changes dependencies and the lockfile. I want to approve both each time.
- **Deny:** reading or editing `.env` (including `cat .env`), force-push, `git reset --hard`, and `rm -rf`.

Without the deny rules, Claude could read `.env` while debugging config and pull real secrets into the conversation. A force-push or hard reset could also permanently wipe commits or uncommitted work. I denied `.env` by exact name instead of `.env.*` so that `.env.example` stays readable.

## Verification

- `/memory`: <!-- TODO: confirm CLAUDE.md shows as loaded -->
- `/permissions`: <!-- TODO: confirm the allow / ask / deny rules are listed -->
- `npm test` (4/4 passing) and `npm run lint` (clean) both run on Node 22.
