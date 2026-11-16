# React

React bindings for Tracking Smith: a Provider, a hook and a router-agnostic
page-view component. Works with React 17, 18 and 19, including Next.js.

[← Back to overview](../README.md)

## Install

```bash
npm install @host-smith/tracking-smith
```

Requires `react` 17, 18 or 19 (already in your app).

## Setup

Wrap your app in the Provider:

```tsx
import { TrackingSmithProvider } from '@host-smith/tracking-smith/react';

export function App() {
  return (
    <TrackingSmithProvider
      config={{
        trackingId: 'ts_your_id',
        apiUrl: 'https://api.tracking-smith.de',
        appVersion: '1.4.0',
      }}
    >
      <Routes />
    </TrackingSmithProvider>
  );
}
```

Track events anywhere below it:

```tsx
import { useTrackingSmith } from '@host-smith/tracking-smith/react';

function SignupButton() {
  const ts = useTrackingSmith();
  return <button onClick={() => ts.track('cta_clicked')}>Sign up</button>;
}
```

## Page views

Render `<TrackingSmithPageView>` with the current URL from your router. It
sends a page view on mount and whenever the URL changes (duplicates are
ignored).

**React Router 6+**

```tsx
import { useLocation } from 'react-router-dom';
import { TrackingSmithPageView } from '@host-smith/tracking-smith/react';

function RouteTracker() {
  const { pathname } = useLocation();
  return <TrackingSmithPageView location={pathname} />;
}
```

**Next.js App Router**

```tsx
// app/tracking.tsx
'use client';
import { usePathname } from 'next/navigation';
import { TrackingSmithProvider, TrackingSmithPageView } from '@host-smith/tracking-smith/react';

export function Tracking({ children }: { children: React.ReactNode }) {
  return (
    <TrackingSmithProvider config={{ trackingId: 'ts_your_id', apiUrl: 'https://api.tracking-smith.de' }}>
      <TrackingSmithPageView location={usePathname()} />
      {children}
    </TrackingSmithProvider>
  );
}
```

Use `<Tracking>` in `app/layout.tsx`. The Provider is SSR-safe: it does
nothing on the server and starts the client in the browser.

**Next.js Pages Router:** `<TrackingSmithPageView location={useRouter().asPath} />`

Alternatively, set `autoTrack: { pageViews: true }` in the config instead
of using the component.

## API

| Export | Description |
|---|---|
| `<TrackingSmithProvider config adapter?>` | Creates the client once and destroys it on unmount. `config` is read at mount; later changes are ignored. |
| `useTrackingSmith()` | Returns the client. Throws if used outside the Provider. |
| `<TrackingSmithPageView location title? referrer?>` | Sends `page()` when `location` changes. Renders nothing. |

The client methods (`track`, `identify`, `page`, `setConsent`, …) and all
config options are listed in the [overview](../README.md#core-api).

## Two builds: classic vs. automatic JSX

| Import | When to use |
|---|---|
| `@host-smith/tracking-smith/react` (default) | Works everywhere, including React 17.0.0. **Use this if unsure.** |
| `@host-smith/tracking-smith/react/automatic` | React 17.0.1+ with `react/jsx-runtime` available. Slightly smaller. |

Both have identical exports and types; switching is a one-line change.

## StrictMode

In development, React 18+ StrictMode runs effects twice, which can leave the
Provider with a stopped client until you reload. Production is not affected.
If this gets in your way during development, move the Provider outside
`<StrictMode>`.

## Compatibility

| | Supported |
|---|---|
| React | 17, 18, 19 |
| Next.js | App Router, Pages Router |
| Routers | Any (React Router, Next.js, TanStack Router, …) |
| Browsers | ES2019+ (Chrome 74+, Firefox 68+, Safari 12.1+, Edge 79+) |
