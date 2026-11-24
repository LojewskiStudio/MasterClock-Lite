# MasterClock Lite

**An independent browser-based time reference for studios, control rooms, and live production environments.**

<img width="1933" height="1080" alt="MasterClock Lite" src="https://github.com/user-attachments/assets/8b02f8fe-7903-4321-a847-2c54c9b3c98d" />

MasterClock Lite is a lightweight, standalone HTML application designed for environments where a clear, continuous and reliable time reference is essential.

Unlike conventional clocks that simply display the computer's system time, MasterClock Lite synchronizes with external UTC time services and maintains its own internal time reference independently of subsequent operating system clock changes.

It combines synchronized time display, millisecond resolution, global time monitoring and operational synchronization diagnostics in a single browser application.

The entire application runs from a single HTML file. No dedicated server, backend, build process or additional software installation is required.

## Features

- **External UTC Time Synchronization** – Acquires a UTC time reference from external time services over HTTPS.
- **Automatic Source Failover** – Maintains synchronization continuity by switching to an alternative synchronization method when the current source becomes unavailable.
- **HTTP Date Fallback** – Provides an additional time reference using the hosting server's HTTP `Date` response header when API sources are unavailable.
- **Continuous Monotonic Timekeeping** – Maintains continuous time independently of subsequent operating system clock changes.
- **Holdover Operation** – Continues running from the last valid time reference when external synchronization sources become temporarily unavailable.
- **Wake & Drift Detection** – Detects significant clock discrepancies and triggers resynchronization when necessary.
- **Automatic & Manual Resynchronization** – Periodically refreshes the time reference and allows manual synchronization on demand.
- **High-Resolution Time Display** – Displays synchronized time down to milliseconds, alongside standard hours, minutes and seconds.
- **Synchronization Telemetry** – Displays device clock offset, estimated uncertainty, network round-trip time (RTT), active time source and synchronization status.
- **Six-Location World Clock** – Displays a primary studio clock alongside six configurable global time zones.
- **Multiple Time Zones** – Supports UTC, local device time and standard IANA time zones.
- **Multilingual Interface** – Automatically detects the browser language and provides a localized interface in multiple languages.
- **Responsive Interface** – Adapts to desktop monitors, control room displays, tablets and mobile devices.
- **Persistent Preferences** – Saves configuration locally in the browser without requiring an account.
- **Cross-Tab Synchronization Coordination** – Shares recent synchronization information between tabs on the same browser origin to reduce redundant requests.
- **Iframe Integration** – Exposes synchronized time information to external applications through a read-only `postMessage` interface.

## Time Synchronization

MasterClock Lite obtains its time reference from external UTC time services.

The synchronization system uses a primary time source, an alternative source when the primary source cannot provide a valid response, and an additional HTTP `Date` fallback when API sources are unavailable.

The application evaluates available time samples and establishes an internal time reference based on the browser's monotonic clock.

Once synchronized, the clock continues running independently of subsequent changes to the computer's system clock.

If external synchronization sources become temporarily unavailable after a valid synchronization, MasterClock Lite enters **Holdover** mode and continues operating from its last valid reference.

This allows the displayed time to remain continuous during temporary network interruptions.

## Synchronization Status

The status indicator provides information about the current synchronization condition:

- **Locked** – A valid external time reference has been acquired and estimated uncertainty is within normal operating thresholds.
- **Warning** – The estimated uncertainty is elevated and the time reference requires attention.
- **Holdover** – The clock continues operating from a previously acquired time reference while synchronization is unavailable or a resynchronization attempt has failed.
- **Unavailable** – No valid time reference has been acquired.
- **Error** – A synchronization error has been detected.

Additional synchronization telemetry includes:

- **Offset** – Difference between the synchronized reference and the local device clock, expressed in milliseconds.
- **Accuracy / Uncertainty** – Estimated synchronization uncertainty, displayed as `±ms`.
- **RTT** – Network request round-trip time, when available.
- **Source** – Currently active time source.
- **Holdover Age** – Time elapsed since the last successful synchronization.

## Iframe Integration – postMessage API

MasterClock Lite can expose its already-calculated synchronized time to another web application through an embedded iframe.

The integration uses the browser's native `postMessage` API and does not require a separate backend or additional software.

The interface is **read-only**. It never initiates synchronization, makes additional network requests or modifies the internal time engine.

The integration is disabled by default and can be enabled using `?embed=true`.

### Basic Integration

```html
<iframe
  id="clock"
  src="https://lojewskistudio.github.io/MasterClock-Lite/?embed=true"
  title="MasterClock Lite"
  width="100%"
  height="480"
></iframe>
```

The recommended height of 480 pixels accommodates the main clock and all six configurable world-clock locations.

### Origin Security

An optional `parentOrigin` URL parameter can restrict communication to a specific embedding origin.

Example:

```text
?embed=true&parentOrigin=https%3A%2F%2Fyour-app.example
```

When specified, only messages from that origin are accepted.

Without `parentOrigin`, the origin of the first valid `MASTERclock_HELLO` handshake is recorded and used for all subsequent communication.

Replies are sent exclusively to the recorded parent origin, never to a wildcard origin.

### Integration Protocol (Version 1)

All messages contain a `type` and `version: 1`.

Messages are accepted only from `window.parent`, using the supported protocol version and an authorized origin.

| Direction | Message | Purpose |
|---|---|---|
| Parent → Clock | `MASTERclock_HELLO` | Initiates the connection |
| Clock → Parent | `MASTERclock_READY` | Confirms the handshake |
| Parent → Clock | `MASTERclock_REQUEST_TIME` | Requests an immediate time update |
| Clock → Parent | `MASTERclock_TIME` | Delivers synchronized time and status information |

The parent should resend `MASTERclock_HELLO` until `MASTERclock_READY` is received.

After a successful handshake, the clock sends periodic time updates at approximately 100 ms intervals.

Opaque origins, such as `file://` and sandboxed frames without `allow-same-origin`, are not supported.

### Available Time Data

Each `MASTERclock_TIME` message provides a structured snapshot of the existing time engine.

Example:

```javascript
{
  type: "MASTERclock_TIME",
  version: 1,
  unixMs: 1795520000123,
  synchronized: true,
  syncState: "locked",
  accuracyMs: 7.8,
  source: "timeapi.io",
  holdover: false,
  holdoverAgeMs: 4210,
  seq: 42,
  monotonicMs: 51234.5
}
```

**Field descriptions:**

- `unixMs` – Current Unix timestamp in milliseconds. Calculated from the iframe's monotonic clock and the internal synchronization offset. `null` when no valid time is available.
- `synchronized` – `true` only when the synchronization state is `locked`.
- `syncState` – Current synchronization state: `locked`, `warning`, `holdover`, `unavailable` or `error`.
- `accuracyMs` – Estimated synchronization uncertainty in milliseconds. `null` when unknown.
- `source` – Name of the synchronization source associated with the active time reference.
- `holdover` – Indicates operation using a previously established time reference.
- `holdoverAgeMs` – Time elapsed since the last successful synchronization.
- `seq` – Incremental message counter.
- `monotonicMs` – The iframe's `performance.now()` value at the time of sampling.

Both `warning` and `holdover` can provide usable time values, but `synchronized` remains `false`.

`unavailable` and `error` indicate that no valid time reference is currently available.

### Integration Security & Operation

The iframe interface only reads the existing internal time reference and synchronization state.

It does not call the synchronization engine, initiate network requests or alter the clock's operation.

Communication is restricted to the embedding parent window and validated against the authorized origin.

An optional `parentOrigin` parameter allows the embedding application to explicitly define the permitted origin.

The integration is intended for applications that need access to a continuously maintained time reference without implementing their own synchronization engine.

### Integration Demo

A live integration demo, including a complete parent-side implementation, is available [here](https://lojewskistudio.github.io/MasterClock-Lite/embed-demo.html).

## Configuration

The settings panel provides access to:

- Primary studio timezone (`UTC`, `Device` or an available IANA timezone).
- Six global hub timezones for world-clock monitoring.
- Interface language.
- 12-hour (`AM / PM`) or 24-hour time format.
- Final-ten-second visual tally pulse.
- Manual resynchronization.

All preferences are stored locally in the browser using `localStorage`.

No account or external configuration is required.

## Browser Application

**[Open MasterClock Lite](https://lojewskistudio.github.io/MasterClock-Lite/)**

MasterClock Lite runs directly in your browser. No installation, downloads or server setup are required.

Internet access is required for external time synchronization. The HTTP `Date` fallback additionally requires the application to be served over HTTP or HTTPS.

## Technical Details

- HTML5 & CSS3
- Vanilla JavaScript (ES6+)
- Fetch API & `AbortController`
- High Resolution Time API
- Internationalization API
- `requestAnimationFrame` rendering
- Web Storage API (`localStorage`, `sessionStorage`)
- Browser `postMessage` API
- Optional Screen Wake Lock API support

**No external JavaScript libraries or frameworks.**

## Important Notes

MasterClock Lite is a browser-based time reference, not a certified precision timekeeping instrument.

Actual synchronization performance depends on external time service accuracy, network latency, path asymmetry, browser scheduling, timer resolution and the hosting environment.

The displayed uncertainty is an estimate derived from network sampling and timing assumptions. It is not a guaranteed accuracy specification.

MasterClock Lite does not modify the operating system clock. It maintains an independent time reference inside the browser.

It is not a substitute for dedicated NTP/PTP infrastructure, a hardware grandmaster or a GPS-disciplined oscillator (GPSDO) in safety-critical or legally binding applications.

For mission-critical broadcast, transmission and playout operations, independent verification against a primary station time reference is recommended.

## License

MIT License.

Copyright (c) 2026 Mihai Lucian Lojewski / Lojewski Studio.

See [LICENSE](LICENSE) for details.

---

## Third-Party Services

This software may use external time services over HTTP/HTTPS to obtain time synchronization data. These services are independently operated by their respective providers and are not part of this software.

Use of these services is subject to their respective terms of service, availability policies and privacy policies.

The software author is not affiliated with, endorsed by or responsible for these third-party services.

---

**MasterClock Lite**

*An independent time reference, directly in your browser.*

Lojewski Studio · 2026
