# Tracking Smith JavaScript SDK

Official JavaScript SDK for [Tracking Smith](https://ja-developer.de/). Send
page views, custom events and user identity from your website or web app to
your Tracking Smith project.

One package covers every setup:

| Your setup | Use | Guide |
|---|---|---|
| Plain HTML, WordPress, any site without a bundler | `<script>` tag from jsDelivr | [vanilla/](vanilla/README.md) |
| React 17 / 18 / 19 (incl. Next.js) | `@host-smith/tracking-smith/react` | [react/](react/README.md) |
| Angular 17+ | `@host-smith/tracking-smith/angular` | [angular/](angular/README.md) |
| Any other bundled app (Vue, Svelte, plain TS…) | `@host-smith/tracking-smith` | [below](#any-bundled-app) |

> **You need a Tracking Smith account.** Create a project on the Tracking
> Smith platform to get your **tracking ID** (`ts_…`) and **API URL**. Use of
> this SDK requires an active Tracking Smith agreement — see [LICENSE](LICENSE).

## Install

```bash
npm install @host-smith/tracking-smith
# or
pnpm add @host-smith/tracking-smith
# or
yarn add @host-smith/tracking-smith
```

Script-tag users don't install anything — see [vanilla/](vanilla/README.md).

## Quick start

### Script tag

```html
<script
  src="https://cdn.jsdelivr.net/npm/@host-smith/tracking-smith@0.1/dist/tracking-smith.min.js"
  data-tracking-id="ts_your_id"
  data-api-url="https://api.tracking-smith.de"
  data-auto-track-page-views="true"
></script>
```

### React

```tsx
import { TrackingSmithProvider, useTrackingSmith } from '@host-smith/tracking-smith/react';

<TrackingSmithProvider config={{ trackingId: 'ts_your_id', apiUrl: 'https://api.tracking-smith.de' }}>
  <App />
</TrackingSmithProvider>;
```

### Angular

```ts
import { provideTrackingSmith, provideTrackingSmithRouter } from '@host-smith/tracking-smith/angular';

export const appConfig = {
  providers: [
    provideRouter(routes),
    provideTrackingSmith({ trackingId: 'ts_your_id', apiUrl: 'https://api.tracking-smith.de' }),
    provideTrackingSmithRouter(),
  ],
};
```

### Any bundled app

```ts
import { createTrackingSmith } from '@host-smith/tracking-smith';

const ts = createTrackingSmith({
  trackingId: 'ts_your_id',
  apiUrl: 'https://api.tracking-smith.de',
  autoTrack: { pageViews: true },
});

ts.track('signup', { plan: 'pro' });
ts.identify('user-42');
```

## Core API

Every integration exposes the same client:

| Method | Purpose |
|---|---|
| `track(type, props?)` | Send a custom event. |
| `page(props?)` | Send a page view (automatic with `autoTrack.pageViews` or the router integrations). |
| `identify(userId)` | Attach your user ID to subsequent events. |
| `setContext(ctx)` | Add context to all subsequent events. |
| `setConsent('granted' \| 'denied' \| 'pending')` | Update the user's consent state. |
| `reset()` | Forget the user (e.g. on logout). |
| `flush()` | Send queued events immediately. |
| `destroy()` | Stop timers and listeners. |

## Configuration

| Option | Type | Default | |
|---|---|---|---|
| `trackingId` | string | — | **Required.** Your project's tracking ID. |
| `apiUrl` | string | — | **Required.** Tracking Smith API URL. |
| `appVersion` | string | — | Stamped on every event. |
| `environment` | string | — | Free-form label, e.g. `production`. |
| `autoTrack.pageViews` | boolean | `false` | Track page views on load and route changes. |
| `autoTrack.clicks` | boolean | `false` | Track clicks on elements with `data-ts-track`. |
| `autoTrack.formSubmits` | boolean | `false` | Track form submissions (field names only, no values). |
| `autoTrack.webVitals` | `() => import('web-vitals')` | — | Track Core Web Vitals (install `web-vitals` yourself). |
| `consent` | `'granted' \| 'denied' \| 'pending'` | `'granted'` | Initial consent state. |
| `respectDoNotTrack` | boolean | `false` | Deny consent when the browser sends Do-Not-Track. |
| `cookieless` | boolean | `false` | Keep nothing on the device (no localStorage/IndexedDB). |
| `beforeSend` | `(event) => event \| null` | — | Modify or drop events before sending. |
| `batchSize` | number | `20` | Events per request. |
| `flushIntervalMs` | number | `5000` | Max time before queued events are sent. |
| `sessionTimeoutMs` | number | `1800000` | Inactivity before a new session starts (30 min). |
| `debug` | boolean | `false` | Log SDK activity to the console. |

## Compatibility

| | Supported |
|---|---|
| Browsers | ES2019+: Chrome 74+, Firefox 68+, Safari 12.1+, Edge 79+ |
| React | 17, 18, 19 (classic and automatic JSX runtime builds) |
| Next.js | App Router and Pages Router (SSR-safe Provider) |
| Angular | 17+ with `rxjs` 7+; `@angular/router` optional |
| Module formats | ESM and CommonJS, TypeScript types included |
| Script tag | IIFE build, exposes `window.TrackingSmith` |

Framework packages are optional peer dependencies — you only need the one you
use.

## Versioning

The SDK follows [semantic versioning](https://semver.org/). While on `0.x`,
minor versions may contain breaking changes; pin with `~0.1.0` (npm) or
`@0.1` (CDN) if you need stability. See [CHANGELOG.md](CHANGELOG.md).

## Support

Found a bug or have a question? [Open an issue](https://github.com/host-smith/tracking-smith-js/issues).
This repository contains documentation and releases only — the SDK source is
not public.

## License

Proprietary. © Ambos Development GmbH. Use requires an active Tracking Smith
agreement — see [LICENSE](LICENSE).
