# AQA Ads

Static website for AQA Ads, the paid-media practice run by Ruben Colon. It is the public site for [aqa-ads.com](https://aqa-ads.com): home, terms of service, privacy policy, and refund and cancellation policy.

There is no build step and no backend. The site is plain HTML and CSS. Newsreader and Source Sans 3 are self-hosted from `fonts/` under the SIL Open Font License (the license files sit next to the font files).

## Pages

| URL | File |
| --- | --- |
| `/` | `index.html` |
| `/terms/` | `terms/index.html` |
| `/privacy/` | `privacy/index.html` |
| `/refunds/` | `refunds/index.html` |

## Run it locally

From this folder:

```bash
python3 -m http.server 8741
```

Open [http://127.0.0.1:8741](http://127.0.0.1:8741).

## Deploy

Use **Netlify**. It is the simplest free option that can serve this site on both `aqa-ads.com` and `www.aqa-ads.com` while DNS stays at Squarespace.

Netlify’s free tier hosts a static site, provisions HTTPS, and redirects between the apex and `www` once both names are added. Squarespace Domains can point the apex at Netlify with a normal A record. You do not move the domain and you do not change nameservers.

### 1. Put the site on Netlify

1. Create a free account at [https://app.netlify.com](https://app.netlify.com).
2. Add a new site.
   - **Drag and drop:** deploy the folder that contains `index.html` (this folder). `index.html` must be at the site root, not inside an extra directory.
   - **Git:** connect the repository. Leave the build command empty. Set the publish directory to `.` (the repository root). `netlify.toml` already sets `publish = "."`.
3. Open the deployed `*.netlify.app` address and confirm all four pages load.

Note the exact hostname Netlify assigns, such as `aqa-ads.netlify.app`. You will use it in DNS.

### 2. Add the custom domain in Netlify first

In the site’s **Domain management**:

1. Add `aqa-ads.com`.
2. Add `www.aqa-ads.com` if Netlify did not add it automatically. Adding either name usually adds the other.
3. Set **`aqa-ads.com`** as the primary domain so `www` redirects to the apex.
4. Leave DNS on Squarespace. Do not switch nameservers to Netlify. Switching nameservers would require recreating every email record by hand.

Netlify will show the records it is waiting for. They should match the table below. If the panel shows a different target, use the panel. The free tier uses the load balancer below. A paid High-Performance Edge plan would show a different target. This site does not need that plan.

### 3. Point Squarespace DNS at Netlify

The domain stays registered at Squarespace. This only changes the website records. Do this after the domain is added in Netlify.

1. Open the [Squarespace domains dashboard](https://account.squarespace.com/domains).
2. Select **aqa-ads.com**.
3. Open **DNS**, then **DNS Settings**.
4. Remove the website records that point the domain at Squarespace, if they are present. Use the trash icon on the grouped **Squarespace defaults** preset, or delete these records individually when you see them:
   - `A` `@` `198.185.159.144`
   - `A` `@` `198.185.159.145`
   - `A` `@` `198.49.23.144`
   - `A` `@` `198.49.23.145`
   - `CNAME` `www` `ext-cust.squarespace.com`
5. Leave every email record in place. Do not delete `MX` records, SPF or DKIM `TXT` records, or Google Workspace verification records. Those are what make `ruben@aqa-ads.com` deliver.
6. Add these two records under **Custom records**. In Squarespace, the apex host can be `@` or left blank.

| Type | Host | Value |
| --- | --- | --- |
| `A` | `@` | `75.2.60.5` |
| `CNAME` | `www` | `your-site-name.netlify.app` |

Replace `your-site-name.netlify.app` with the hostname from step 1. Enter it with no `https://` and no path.

Squarespace also has **Add preset → Netlify**. You can use it, then check the result against the table. The preset does not know your site name, so confirm the `www` CNAME points at your real `*.netlify.app` hostname. When you are done there should be **one** `A` record on `@`, pointing at `75.2.60.5`. A second apex `A` record, or an `ALIAS` to `apex-loadbalancer.netlify.com` alongside this `A` record, will keep HTTPS from provisioning.

### 4. Wait for HTTPS

DNS can take up to 48 hours to propagate, and is often faster. When Netlify reports the domain as verified, it issues a certificate on its own. Then check:

- [https://aqa-ads.com](https://aqa-ads.com)
- [https://www.aqa-ads.com](https://www.aqa-ads.com) (should redirect to the apex)

Sources for these targets: [Netlify’s external DNS guide](https://docs.netlify.com/manage/domains/configure-domains/configure-external-dns/) (apex fallback `A` record `75.2.60.5`) and [Squarespace’s DNS records guide](https://support.squarespace.com/hc/en-us/articles/31119879125645-DNS-records-for-web-hosting) (`A`, `CNAME`, and the Netlify preset).

## Read before you publish

The pages are written to be published as-is, and a few business decisions are still Ruben’s:

- Confirm `ruben@aqa-ads.com` receives mail before the site is the public contact.
- Read the pricing lines, especially how “10% of monthly ad spend” is calculated and when a billing period ends.
- Have a lawyer review the terms, privacy policy, and refund policy before relying on them. They are a plain-language starting point for a Massachusetts B2B services practice, not a legal opinion.
