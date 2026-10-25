# Ethan Kreuzer — portfolio

A Cloudflare Worker hosting Ethan's landscape portfolio and its content editor.

## Editing the website

Open https://ethankreuzer.com/admin and sign in with the admin password.
Edit names, text, profile links, email addresses, cards, project details, or pictures, then choose **Save changes**. Changes appear immediately; GitHub builds are only needed for application-code changes.

Pictures are resized in your browser and stored in the Cloudflare D1 database. JPEG, PNG, and WebP are supported. Each uploaded result is capped at 900 KB; the picture library is capped at 50 MB. The editor can download a JSON content backup.

## Cloudflare configuration

- Worker: `portfolio`
- Main module: `worker.mjs`
- D1 binding: `DB` (configured in `wrangler.jsonc`)
- Required encrypted Worker secret: `ADMIN_PASSWORD` — use a unique password of at least 16 characters. Set it in Cloudflare; never put it in this repository.
- Deploy command: `npx wrangler deploy`
- Database tables initialize automatically on the first content request.

Keep the Workers **Free** plan. Free-tier limits apply; no paid services or paid overages are enabled by this source. If the account is moved to a paid plan, review its billing settings separately.

The editor uses secure, HttpOnly, SameSite cookies, origin checks, server-side validation, login throttling, and revision checks to prevent accidental overwrites. Rotating ADMIN_PASSWORD signs out existing editor sessions. There is no public password-setup endpoint.

## Files

`worker.mjs` is a self-contained deployment bundle containing the public portfolio, editor, scene artwork, and server handlers. `wrangler.jsonc` declares the Worker and database. `package.json` supplies Wrangler for Cloudflare's GitHub build integration.

Content and uploaded pictures live in D1 and are not overwritten by future code deployments. Use the editor's backup button to export content.
