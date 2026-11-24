# MasterClock Lite

**A browser-based time reference for studios, control rooms, and live production environments.**

<img width="1933" height="1080" alt="MasterClock Lite" src="https://github.com/user-attachments/assets/8b02f8fe-7903-4321-a847-2c54c9b3c98d" />

MasterClock Lite is a lightweight, standalone HTML application designed for environments where a clear, continuous and reliable time reference is essential.

Unlike conventional clocks that simply display the computer's system time, MasterClock Lite synchronizes with external UTC time services and maintains its own internal time reference independently of the operating system clock.

It combines synchronized time display, millisecond resolution, global time monitoring and operational synchronization diagnostics in a single browser application.

The entire application runs from a single HTML file. No dedicated server, backend, build process or additional software installation is required.

## Features

- **External UTC Time Synchronization** – Acquires a UTC time reference from external time services over HTTPS.
- **Automatic Source Failover** – Automatically maintains synchronization continuity by switching to an alternative synchronization method when the current one becomes unavailable.
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
- **Iframe Integration** – Makes synchronized time information available to external applications through a read-only `postMessage` interface.

## Time Synchronization

MasterClock Lite obtains its time reference from external UTC time services.

The synchronization system uses a primary time source, an alternative source when the primary source cannot provide a valid response, and an additional HTTP `Date` fallback when API sources are unavailable.

The application evaluates available time samples and establishes an internal time reference based on the browser's monotonic clock.

Once synchronized, the clock continues running independently of the computer's system clock.

If all external sources become temporarily unavailable after a valid synchronization, MasterClock Lite enters **Holdover** mode and continues operating from its last valid reference.

## Synchronization Status

The status indicator provides real-time information about the current synchronization state:

- **Locked** – A valid external time reference has been acquired and synchronization is within normal operating thresholds.
- **Warning** – Estimated uncertainty is elevated, synchronization sources are unavailable, or the clock is operating in Holdover mode.
- **Unavailable** – No valid time reference has been acquired.

Additional synchronization telemetry includes:

- **Offset** – Difference between the synchronized reference and the local device clock, expressed in milliseconds.
- **Accuracy / Uncertainty** – Estimated synchronization uncertainty, displayed as `±ms`.
- **RTT** – Network request round-trip time, when available.
- **Source** – Currently active time source.
- **Holdover** – Indicates continued operation from a previously acquired time reference.

## Iframe Integration

MasterClock Lite can provide its synchronized time reference to another web application through an embedded iframe.

The integration uses the browser's native `postMessage` API and does not require a separate backend or additional software.

To enable the integration, add `?embed=true` to the application URL:

```html
<iframe
  src="https://lojewskistudio.github.io/MasterClock-Lite/?embed=true"
  title="MasterClock Lite"
></iframe>
```

### Integration Protocol

The embedded interface uses a versioned message protocol:

- `MASTERclock_HELLO` – Initiates communication between the host application and MasterClock Lite.
- `MASTERclock_READY` – Confirms that the embedded interface is ready.
- `MASTERclock_REQUEST_TIME` – Requests a time update.
- `MASTERclock_TIME` – Provides the current time reference and synchronization information.

Time updates are transmitted periodically, with an interval of approximately 100 milliseconds.

### Available Time Data

The integration can provide:

- `unixMs` – Current Unix timestamp in milliseconds.
- `synchronized` – Indicates whether a valid time reference has been acquired.
- `syncState` – Current synchronization state.
- `accuracyMs` – Estimated synchronization uncertainty in milliseconds.
- `source` – Active time source.
- `holdover` – Indicates whether the clock is operating in Holdover mode.
- `holdoverAgeMs` – Time elapsed since the last valid synchronization reference.

### Security and Operation

The iframe interface is read-only. It reports the existing internal time reference without initiating synchronization requests or modifying the clock engine.

Communication is restricted to the embedding parent window and validated against the expected origin. An optional `parentOrigin` URL parameter can be used to explicitly pin the permitted parent origin.

The integration is intended for applications that need access to a continuously maintained time reference without implementing their own time synchronization engine.

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

Internet access is required for external time synchronization. The HTTP Date fallback additionally requires the application to be served over HTTP or HTTPS.

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

Use of these services is subject to their respective terms of service, availability policies, and privacy policies.

The software author is not affiliated with, endorsed by, or responsible for these third-party services.

---

**MasterClock Lite**

*An independent time reference, directly in your browser.*

Lojewski Studio · 2026
