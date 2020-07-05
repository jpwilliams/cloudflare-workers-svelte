# Cloudflare Workers Svelte Template

This is a super-simple template for deploying a [Svelte](https://svelte.dev/) site with [TypeScript](https://www.typescriptlang.org/) support to [Cloudflare Workers Sites](https://workers.cloudflare.com/sites). It includes automated deployment using [GitHub Actions](https://github.com/features/actions), the configuration for which can be found at [`.github/workflows/deploy.yaml`](https://github.com/jpwilliams/cloudflare-workers-svelte/blob/master/.github/workflows/deploy.yaml).

An example of this exact repository is hosted at [svelte.jpwilliams.dev](https://svelte.jpwilliams.dev).

A version _without_ [TypeScript](https://www.typescriptlang.org/) can be found at the [js](https://github.com/jpwilliams/cloudflare-workers-svelte/tree/js) branch.

## Making it your own

1. Add your Cloudflare [`account_id`](https://github.com/jpwilliams/cloudflare-workers-svelte/blob/master/wrangler.toml#L3) and [`zone_id`](https://github.com/jpwilliams/cloudflare-workers-svelte/blob/master/wrangler.toml#L6) to [`wrangler.toml`](https://github.com/jpwilliams/cloudflare-workers-svelte/blob/master/wrangler.toml).
2. _(Optional)_ For automated deployments, add a `CF_API_TOKEN` secret to your GitHub repository which should be a Cloudflare API Token with the "_Edit Cloudflare Workers_ template.

## Use

- `npm install` to install dependencies
- `npm run dev` to start a dev server
- `npm run format` to format your code
- `wrangler publish` to deploy

## Expansion

To pad out your app with more complex features, take a look at the following files, repositories, and sites:

- [`workers-site/index.js`](https://github.com/jpwilliams/cloudflare-workers-svelte/blob/master/workers-sites/index.js) handles how the Workers fetch your static content
- [`wrangler.toml`](https://github.com/jpwilliams/cloudflare-workers-svelte/blob/master/wrangler.toml) is used to configure deployment to Cloudflare using [cloudflare/wrangler](https://github.com/cloudflare/wrangler)
- [`.github/workflows/deploy.yaml`](https://github.com/jpwilliams/cloudflare-workers-svelte/blob/master/.github/workflows/deploy.yaml) is used to configure automated deployments using [cloudflare/wrangler-action](https://github.com/cloudflare/wrangler-action)
- [Svelte](https://svelte.dev/)
- [Cloudflare Workers Sites](https://workers.cloudflare.com/sites)

## The template explained

I usually hate using template repositories that aren't my own for no other reason than I have no idea what's going on; they're often bloated and starting a project with one feels like setting yourself up for a trap.

For your sake (and mine in 6 months' time) I'll explain what's actually going on in this repository and how it all stitches together.

---

This is an amalgamation of [sveltejs/template](https://github.com/sveltejs/template) and a generic [Workers Sites](https://workers.cloudflare.com/sites) repository created using [cloudflare/wrangler](https://github.com/cloudflare/wrangler). This results in a project with two faces: the main Svelte project in the root and a Workers Sites project inside the [`workers-sites`](https://github.com/jpwilliams/cloudflare-workers-svelte/blob/master/workers-sites) directory.

Atfer that, I've added:

- Automated deployments
- Prettier for both projects
- TypeScript for both projects

---

### Automated deployments

A simple `deploy.yaml` file has been specified at `.github/workflows/deploy.yaml` that will run `npm ci` to install dependencies, `npm run build` to build the Svelte project, and finally use [cloudflare/wrangler-action](https://github.com/cloudflare/wrangler-action) to essentially run `wrangler build` and `wrangler publish`.

The automated deployments can be removed completely by deleting the file (or just not using GitHub), or can be configured further by editing it.

### Prettier

I don't like Prettier. It's my opinion that it farts out ugly code. I'm a hipster dev and I like my own rules and I like coding without semi-colons. Thing is, it doesn't matter what I like; with Prettier, everyone writes the same way, and I like that better.

Both projects have the `prettier` dev dependency added as well as a `format` script that can be used via `npm run format`. You could add a pre-commit hook to run this, perhaps. The Svelte project also has [sveltejs/prettier-plugin-svelte](https://github.com/sveltejs/prettier-plugin-svelte) so we can format `.svelte` files too.

### TypeScript

For this, there are two `tsconfig.json` files. One at the root and one at `workers-sites/tsconfig.json`. Two are required here because the two projects, though they're deployed as one, need to parse the TypeScript differently with different types. The Workers Sites project requires `WebWorker` types, for example, whereas the Svelte project needs to understand TypeScript inside `.svelte` files.

#### Svelte

Here, the following dev dependencies have been added:

- `tslib` and `typescript` for actually parsing TypeScript.
- `@rollup/plugin-typescript` for parsing all TypeScript for RollUp.
- `svelte-preprocess` for parsing TypeScript inside `.svelte` files with `<script lang="typescript">`.
- `@pyoner/svelte-ts-preprocess` for helping code editors to understand the parsing in `.svelte` files.

On top of this, `@rollup/plugin-typescript` and `svelte-preprocess` have been added to our `rollup.config.js` file to make sure it makes use of them. Also, a `svelte.config.js` file has been added which uses `@pyoner/svelte-ts-preprocess` to help with code editor parsing.

Finally, a very simple `tsconfig.json` has been added. The only notable property here is that we've added the `svelte` types to the `compilterOptions` block.

#### Workers Sites

Workers Sites use Webpack internally to build and deploy code. Because of this, you can manually specify a `webpack.config.js` file to use for this, meaning you can customise the build process as needed.

The following dev dependencies have been added:

- `typescript` for actually parsing TypeScript.
- `ts-loader` for loading `.ts` files with Webpack.
- `webpack` and `webpack-cli` for our custom build.

`webpack.config.js` inside `workers-sites` contains the simple Webpack config. It loads any `.ts` or `js` files, transpiles them and outputs the whole lot to `dist/worker.js` which gets uploaded to Cloudflare when `wrangler publish` is run.

Wrangler doesn't pick up this config automatically, so we have to manually point to it by adding a `webpack_config` entry to `wrangler.toml` at the root of the project.
