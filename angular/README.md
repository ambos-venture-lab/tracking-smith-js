# Angular

Angular bindings for Tracking Smith: an environment provider, an injectable
service and optional Router integration. Works with Angular 17+.

[← Back to overview](../README.md)

## Install

```bash
npm install @host-smith/tracking-smith
```

Requires `@angular/core` 17+ and `rxjs` 7+ (already in your app).
`@angular/router` is only needed for automatic page views.

## Setup

Register the providers in your application config:

```ts
// app.config.ts
import { ApplicationConfig } from '@angular/core';
import { provideRouter } from '@angular/router';
import {
  provideTrackingSmith,
  provideTrackingSmithRouter,
} from '@host-smith/tracking-smith/angular';
import { routes } from './app.routes';

export const appConfig: ApplicationConfig = {
  providers: [
    provideRouter(routes),
    provideTrackingSmith({
      trackingId: 'ts_your_id',
      apiUrl: 'https://api.tracking-smith.de',
      appVersion: '1.4.0',
    }),
    provideTrackingSmithRouter(), // page view on every navigation
  ],
};
```

Using NgModules? Add the same two calls to the `providers` array of your
root `AppModule`.

## Tracking events

Inject `TrackingSmithService` anywhere:

```ts
import { Component, inject } from '@angular/core';
import { TrackingSmithService } from '@host-smith/tracking-smith/angular';

@Component({
  selector: 'app-signup-button',
  standalone: true,
  template: `<button (click)="onSignup()">Sign up</button>`,
})
export class SignupButtonComponent {
  private readonly ts = inject(TrackingSmithService);

  onSignup(): void {
    this.ts.track('cta_clicked', { label: 'signup' });
  }
}
```

## API

| Export | Description |
|---|---|
| `provideTrackingSmith(config)` | Registers `TrackingSmithService`. Call once in the root providers. |
| `provideTrackingSmithRouter()` | Sends a page view on every `NavigationEnd`, using the final URL after redirects. Requires `provideRouter()`. |
| `TrackingSmithService` | Injectable with `track`, `identify`, `page`, `setContext`, `setConsent`, `reset`, `flush`. Cleans up automatically when the app is destroyed. |
| `DATA_SMITH_CONFIG` | Injection token holding the config. |

Not using Angular Router? Leave out `provideTrackingSmithRouter()` and call
`ts.page()` yourself.

All config options are listed in the [overview](../README.md#configuration).

## Consent

```ts
provideTrackingSmith({
  trackingId: 'ts_your_id',
  apiUrl: 'https://api.tracking-smith.de',
  consent: 'pending',
});

// later, in your cookie banner:
inject(TrackingSmithService).setConsent('granted');
```

## Server-side rendering

The service is built for the browser. With Angular SSR, provide
`provideTrackingSmith()` only in the browser config (`app.config.ts`), not in
`app.config.server.ts`.

## Compatibility

| | Supported |
|---|---|
| Angular | 17+ (standalone and NgModule apps) |
| rxjs | 7+ |
| `@angular/router` | Optional, 17+ |
| Browsers | ES2019+ (Chrome 74+, Firefox 68+, Safari 12.1+, Edge 79+) |
