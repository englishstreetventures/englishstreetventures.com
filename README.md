# English Street Ventures

One-page Astro site for English Street Ventures, built as static output and served by Cloudflare Workers Static Assets.

## Development

```sh
bun install
bun run dev
```

Build the static site into `dist/` with `bun run build`. Preview the generated output through Wrangler locally with `bun run preview`.

## Deploy

`bun run deploy` builds the site and deploys the `englishstreetventures-com` Worker. Wrangler is configured for both the `workers.dev` preview URL and the `englishstreetventures.com` custom domain. Cloudflare account access and a configured domain are required for a live deployment.
