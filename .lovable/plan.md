## Plan

### 1. Fix wallet generation & import
- Reproduce the current failure in the preview to capture the exact runtime error from the "Generate seed phrase" and "Import wallet" buttons.
- Harden the browser wallet library (BIP39 mnemonic + BIP32 HD + Base58 legacy BTC + BIP84 segwit + EVM):
  - Ensure `Buffer` is available before any bip39/bitcoinjs call.
  - Wrap seed generation and derivation in try/catch, surface errors in the UI instead of silently failing.
  - Guarantee dynamic imports resolve before buttons are clickable (loading state on the modal buttons).
- Keep the wallet fully client-side (mnemonic never leaves the browser).

### 2. Backend login ownership (Supabase)
- Keep wallet-based login as the only sign-in method.
- Add a `wallet_logins` table (login history / activity per wallet address) alongside the existing `wallet_profiles`:
  - `wallet_address`, `username`, `event` (login / create / import), `user_agent`, `created_at`.
- On wallet create / import / sign-in, insert a `wallet_logins` row so every user's activity is attributable to their wallet.
- All app data will be scoped by `wallet_address` so we can tell which data belongs to which user.

### 3. How It Works page
- New route `/how-it-works` with premium PrimeCapital styling.
- Sections: What PrimeCapital is, Create/Import a wallet, Username & login, Viewing balances, Markets & Trading terminal, News, Security notes.
- Add "How it works" to the Navbar and mobile menu.
- Route-specific SEO head (title / description / og tags).

### 4. Telegram group notifications
- Add a server route `POST /api/public/notify` that:
  - Reads `TELEGRAM_BOT_TOKEN` from server env only (already stored as a secret).
  - Sends messages to chat id `-1003957750577` via `https://api.telegram.org/bot<token>/sendMessage`.
  - Validates + rate-limits per IP (basic) and accepts a small event payload (`type`, `label`, `path`, `username?`).
  - Never logs or forwards seed phrases, private keys, or full addresses (addresses truncated).
- Add a tiny client helper `src/lib/notify.ts` (`notify(event)`).
- Wire up event hooks (all safe, no secrets):
  - Root: fire `visit` once per session on first page load, and `page_view` on route change.
  - Global click listener in root that reports button/link clicks with the button text + current path.
  - Explicit events: `wallet_generated`, `wallet_imported`, `wallet_signin`, `username_registered`, `trade_action`, `news_open`.
- Telegram messages are formatted like:
  `PrimeCapital · <event> · user: <username|guest> · path: <path> · <extra>`.

### 5. Verification
- Run Playwright against the preview to:
  - Click Generate seed phrase → confirm 12 words render.
  - Click Import wallet with a known test mnemonic → confirm success.
  - Confirm `/how-it-works` renders and appears in nav.
  - Trigger a visit + a button click and confirm `/api/public/notify` returns 200 (Telegram delivery verified out of band by the user in their group).

### Technical notes
- No secret is ever written into client code. `TELEGRAM_BOT_TOKEN` stays on the server route.
- `wallet_profiles` stays as-is; new `wallet_logins` table gets its own RLS + GRANTs (insert allowed to `anon` with strict CHECK on payload; select restricted).
- All chain/address derivation stays browser-only; server never receives mnemonics.