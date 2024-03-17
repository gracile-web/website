# Output modes (SPA, MPA, SSG, SSR…)

MPA / SSR is the base on which Gracile is built on.  
SSG is technically ahead of time SSR.  
Server is fully dynamic, it fits within your existing setup, consuming
the Gracile outputted handler.

# SPA

Just put one route and implement your own client side router.  
It's not a "mode" per se.

# MPA

Every page will get it's own assets splitting.
Can be paired with your own client side router too.
It's not a "mode" per se.

# Static mode (SSG)

With SSG you get access to more context infos like `props` and `params`.  
Also, you will need to populate `staticPaths` for dynamic routes.

# Server mode (soon)

A handler is provided to do upfront work before rendering the page.

> [!NOTE]
> Server handler is only available in **development mode** for now,
> but after experimentation, a built output will be available to integrate
> into any `Request` / `Response` compliant HTTP server (or with an adapter).

## Pre-rendering

Probably Gracile will not support pre-rendering like Nuxt or Astro does.  
This is because "Stale-While-Revalidate" and "Content Cache" policies became widespread.  
You can serve cached page very efficiently with Vercel, Netlify, CloudFlare or
Nginx.

Remix or Fresh are doing this approach sucessfully, so unless a good reason
is found for this feature, there should be no need for that.

Also combining [prefetch on hover/focus](/docs/add-ons/prefetch/) on links
can pre-trigger the necessary work and "revive" expired cache before the user get
access to the content, if it's quick enough. You could probably shave ~300ms with this.

### Nginx web server

[Use `proxy_cache_background_update` with **Nginx**](https://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_cache_background_update)

```nginx
proxy_cache_key $scheme://$host$uri$is_args$query_string;
proxy_cache_valid 200 10m;
proxy_cache_bypass $arg_should_bypass_cache_xyz1234;
proxy_cache_use_stale updating error timeout http_500 http_502 http_503 http_504 http_429;
proxy_cache_lock on;
proxy_cache_background_update on;
```

This is an example snippet. You should customize it further, like targeting mostly static routes, where data freshness is not critical (e.g. not on multi-page forms).

### "Serverless" platforms

- [**Cloudflare** Cache Control revalidation](https://developers.cloudflare.com/cache/concepts/cache-control/#revalidation)
- [**Netlify**'s SWR](https://www.netlify.com/blog/swr-and-fine-grained-cache-control/)
- [**Vercel**'s SWR](https://vercel.com/docs/edge-network/caching)
