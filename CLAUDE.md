# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

A small Express REST API (users + health check) backed by an in-memory store.

## Commands

- `npm run dev`: start the API on http://localhost:3000 with auto-reload (`node --watch`)
- `npm test`: run all tests with Node's built-in runner (`node --test`)
- `node --test --test-name-pattern="404" tests/users.test.js`: run a single test by name
- `npm run lint`: run ESLint (`eslint:recommended`)

CI (`.github/workflows/ci.yml`) runs lint, then tests, on Node 22. Both must pass.

## Conventions

- Use CommonJS (`require` / `module.exports`), not ES modules. ESLint is set to `sourceType: "script"`.
- Use functions exported from `db/store.js` for data access, not direct reads or writes of the `users` array in routes. To add data behavior, add a function there.
- Use JSON `{ error: "<message>" }` with a specific status (400 for invalid input, 404 for not found), not plain-text or generic 500 responses. Successful creates return 201.
- Use `node:test` + `node:assert` + `supertest` against the exported `app`, not Jest/Mocha or a real running server.
- Put tests in `tests/` as `*.test.js`.

## Architecture

- `server.js` builds the Express app, mounts one router per resource (`/users`, `/health`), and exports `app`. It only calls `listen()` when run directly (`require.main === module`), so tests can import it without opening a port.
- `routes/<resource>.js`: one `express.Router()` per resource. A new resource gets its own file, mounted in `server.js`.
- `db/store.js`: a module-level in-memory array that stands in for a database. It resets on restart. State is shared across all tests in a file, so tests that create users affect later reads.
- Config comes from environment variables (`PORT` only). `.env.example` documents them. The real `.env` is git-ignored.
