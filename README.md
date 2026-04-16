# SvelteKit on Cloudflare — varlock example

A minimal [SvelteKit](https://svelte.dev/docs/kit) app deployed to [Cloudflare Workers](https://developers.cloudflare.com/workers/), using [varlock](https://varlock.dev) for env var management and [`@varlock/1password-plugin`](https://varlock.dev/plugins/1password) to pull secrets from 1Password.

This example exists primarily as a fixture for testing Cloudflare's CI build/deploy pipeline with varlock. It is extracted from the [varlock-examples](https://github.com/dmno-dev/varlock-examples) monorepo into its own git repo so Cloudflare can clone and build it standalone.

## What it demonstrates

- Schema-driven env vars defined in [.env.schema](.env.schema) using [`@env-spec`](https://varlock.dev/env-spec)
- Generated TypeScript types for env vars (via `@generateTypes`)
- Secret injection from 1Password at build/deploy time via `@varlock/1password-plugin`
- The [`@varlock/cloudflare-integration`](https://varlock.dev/integrations/cloudflare) wired into [svelte.config.js](svelte.config.js) and [vite.config.ts](vite.config.ts)
- Typed `ENV` imports in both [server](src/routes/+page.server.ts) and [client](src/routes/+page.svelte) code, with leak-detection for `@sensitive` vars

## Running locally

```sh
pnpm install
pnpm dev
```

You'll need a `.env.local` with `OP_TOKEN` set to a valid 1Password service account token (or comment out the 1Password-backed items in [.env.schema](.env.schema)).

## Building / deploying

```sh
pnpm build           # produces a Cloudflare Worker bundle via @sveltejs/adapter-cloudflare
pnpm deploy          # runs `varlock-wrangler deploy` — injects resolved env before wrangler runs
```

When deployed via Cloudflare's Git integration, set the build command to `pnpm build` and provide `OP_TOKEN` (and any other non-1Password secrets) as environment variables in the Cloudflare dashboard.
