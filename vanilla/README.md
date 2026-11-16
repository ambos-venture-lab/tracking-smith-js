# Script tag (vanilla JS, WordPress)

Add Tracking Smith to any website with one `<script>` tag — no bundler, no
build step. Works for plain HTML, WordPress, Shopify themes, CMS templates
and so on.

[← Back to overview](../README.md)

## Setup

Put this in the `<head>` of every page:

```html
<script
  src="https://cdn.jsdelivr.net/npm/@host-smith/tracking-smith@0.1/dist/tracking-smith.min.js"
  data-tracking-id="ts_your_id"
  data-api-url="https://api.tracking-smith.de"
  data-auto-track-page-views="true"
></script>
```

The script initialises itself and exposes the client as
`window.TrackingSmith`.

**Version pinning:** `@0.1` gets bug fixes automatically. Pin an exact
version (`@0.1.0`) if you want no changes at all.

## Tracking events

```html
<script>
  window.TrackingSmith.track('signup', { plan: 'pro' });
  window.TrackingSmith.identify('user-42');
  window.TrackingSmith.page({ url: '/checkout', title: 'Checkout' });
</script>
```

Clicks and form submissions can also be tracked without code — see
[Autotracking](#autotracking).

## Configuration

Two ways to configure, which can be combined. `window.trackingSmithConfig`
wins on conflict.

### 1. `data-*` attributes

| Attribute | Option | Type |
|---|---|---|
| `data-tracking-id` | `trackingId` | string (required) |
| `data-api-url` | `apiUrl` | string (required) |
| `data-app-version` | `appVersion` | string |
| `data-environment` | `environment` | string |
| `data-auto-track-page-views` | `autoTrack.pageViews` | boolean |
| `data-consent` | `consent` | `granted` / `denied` / `pending` |
| `data-cookieless` | `cookieless` | boolean |
| `data-respect-do-not-track` | `respectDoNotTrack` | boolean |
| `data-debug` | `debug` | boolean |
| `data-batch-size` | `batchSize` | number |
| `data-flush-interval-ms` | `flushIntervalMs` | number |
| `data-session-timeout-ms` | `sessionTimeoutMs` | number |
| `data-max-queue-bytes` | `maxQueueBytes` | number |
| `data-max-retries` | `maxRetries` | number |

Booleans: `"true"`, `"1"` or an empty attribute mean true; `"false"`, `"0"`
mean false.

### 2. `window.trackingSmithConfig`

Needed for options that can't be written as attributes, such as `beforeSend`
or click/form autotracking. Define it **before** the SDK script:

```html
<script>
  window.trackingSmithConfig = {
    trackingId: 'ts_your_id',
    apiUrl: 'https://api.tracking-smith.de',
    autoTrack: { pageViews: true, clicks: true, formSubmits: true },
    beforeSend: (event) => {
      // remove personal data, or return null to drop the event
      return event;
    },
  };
</script>
<script src="https://cdn.jsdelivr.net/npm/@host-smith/tracking-smith@0.1/dist/tracking-smith.min.js"></script>
```

See the [full option list](../README.md#configuration).

## Autotracking

- **Page views** — `autoTrack.pageViews: true` tracks the initial load and
  client-side navigation (History API, hash changes).
- **Clicks** — `autoTrack.clicks: true` tracks clicks on any element with a
  `data-ts-track` attribute. The attribute value becomes the event name:

  ```html
  <button data-ts-track="cta_clicked">Start free trial</button>
  ```

- **Forms** — `autoTrack.formSubmits: true` sends `form_submitted` with the
  form's id, name, action, method and field names. Field **values are never
  sent**. Add `data-ts-no-capture` to a `<form>` to skip it.

## Consent

Start with consent pending and update it from your cookie banner:

```html
<script
  src="https://cdn.jsdelivr.net/npm/@host-smith/tracking-smith@0.1/dist/tracking-smith.min.js"
  data-tracking-id="ts_your_id"
  data-api-url="https://api.tracking-smith.de"
  data-consent="pending"
></script>
<script>
  // in your banner's "accept" handler:
  window.TrackingSmith.setConsent('granted');
</script>
```

For deployments that must not store anything on the device, set
`data-cookieless="true"`. Trade-off: every page load counts as a new
visitor and session.

## WordPress

Add the snippet to your theme's `header.php` before `</head>`, or use a
header/footer code plugin (e.g. *WPCode*) and paste it into the header
section.

## Troubleshooting

- **`window.TrackingSmith` is `undefined`** — `trackingId` or `apiUrl` is
  missing. The SDK logs one warning in the browser console and does nothing;
  it never breaks your page.
- **Nothing arrives** — set `data-debug="true"` and check the console and the
  Network tab for requests to your API URL.

## Browser support

ES2019+: Chrome 74+, Firefox 68+, Safari 12.1+, Edge 79+. Internet Explorer
is not supported.
