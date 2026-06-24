# Internal Sites

Give your company a simple place to deploy internal static sites.

Employees go to:

```txt
https://internal-company.com/deploy
```

They upload a folder or ZIP and get a URL like:

```txt
https://my-site.internal-company.com
```

Every site requires company login.

## Deploy to Cloudflare

[![Deploy to Cloudflare](https://deploy.workers.cloudflare.com/button)](https://deploy.workers.cloudflare.com/?url=https://github.com/dinasaur404/static-sites-plat)

After deploy, finish two account-level steps:

1. Add an Access policy to the deployed Worker.
2. Add routes for your platform domain.

## Set Up

### 1. Deploy this app

Use the Deploy to Cloudflare button above.

During setup, configure:

```txt
SITE_DOMAIN=internal-company.com
DEPLOY_PATH=/deploy
DISPATCH_NAMESPACE_NAME=internal-sites
```

You will also need a secret named:

```txt
DISPATCH_NAMESPACE_API_TOKEN
```

This token needs permission to deploy Workers in your account:

```txt
Account: Workers Scripts: Edit
```

If deploying manually:

```bash
npm install
npx wrangler d1 create internal-sites-platform
npx wrangler dispatch-namespace create internal-sites
npx wrangler secret put DISPATCH_NAMESPACE_API_TOKEN
npx wrangler deploy
```

### 2. Require company login

Create one Access policy for the deployed Worker.

In the Cloudflare dashboard:

1. Go to **Zero Trust** > **Access** > **Applications**.
2. Create a **Self-hosted** application.
3. Choose **Worker** as the destination.
4. Select this deployed Worker.
5. Allow your company users.
6. Choose your company identity provider.

This makes users sign in before they can use the deploy page or view any deployed site.

### 3. Attach your platform domain

Route your deploy page and site subdomains to the Worker:

```toml
routes = [
  { pattern = "internal-company.com/deploy*", zone_name = "internal-company.com" },
  { pattern = "*.internal-company.com/*", zone_name = "internal-company.com" }
]
```

Create DNS records for:

```txt
internal-company.com
*.internal-company.com
```

Now employees can deploy from:

```txt
https://internal-company.com/deploy
```

And sites will be available at:

```txt
https://site-name.internal-company.com
```

## Use

1. Open `/deploy`.
2. Enter a site name.
3. Upload a folder or ZIP.
4. Click **Deploy site**.
5. Open or copy the generated URL.

The uploaded folder or ZIP must include an `index.html`.

## Local Testing

For local testing without company login:

```txt
DISABLE_ACCESS_IDENTITY_CHECK=true
```

In local/demo mode, site URLs use:

```txt
/sites/site-name/
```

Production should use wildcard subdomains:

```txt
https://site-name.internal-company.com
```
