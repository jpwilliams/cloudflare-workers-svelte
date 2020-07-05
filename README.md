# Cloudflare Workers Svelte Template

This is a super-simple template for deploying a [Svelte](https://svelte.dev/) site with [Cloudflare Workers Sites](https://workers.cloudflare.com/sites). It includes automated deployment using [GitHub Actions](https://github.com/features/actions), the configuration for which can be found at [`.github/workflows/deploy.yaml`](https://github.com/jpwilliams/cloudflare-workers-svelte/blob/master/.github/workflows/deploy.yaml).

### Making it your own

1. Add your Cloudflare [`account_id`](https://github.com/jpwilliams/cloudflare-workers-svelte/blob/master/wrangler.toml#L3) and [`zone_id`](https://github.com/jpwilliams/cloudflare-workers-svelte/blob/master/wrangler.toml#L6) to [`wrangler.toml`](https://github.com/jpwilliams/cloudflare-workers-svelte/blob/master/wrangler.toml).
2. _(Optional)_ For automated deployments, add a `CF_API_TOKEN` secret to your GitHub repository which should be a Cloudflare API Token with the "_Edit Cloudflare Workers_ template.

## Use

1. `npm install`
2. `wrangler publish`

### Expansion

To pad out your app with more complex features, take a look at the following files, repositories, and sites:

- [`workers-site/index.js`](https://github.com/jpwilliams/cloudflare-workers-svelte/blob/master/workers-sites/index.js) handles how the Workers fetch your static content
- [`wrangler.toml`](https://github.com/jpwilliams/cloudflare-workers-svelte/blob/master/wrangler.toml) is used to configure deployment to Cloudflare using [cloudflare/wrangler](https://github.com/cloudflare/wrangler)
- [`.github/workflows/deploy.yaml`](https://github.com/jpwilliams/cloudflare-workers-svelte/blob/master/.github/workflows/deploy.yaml) is used to configure automated deployments using [cloudflare/wrangler-action](https://github.com/cloudflare/wrangler-action)
- [Svelte](https://svelte.dev/)
- [Cloudflare Workers Sites](https://workers.cloudflare.com/sites)
