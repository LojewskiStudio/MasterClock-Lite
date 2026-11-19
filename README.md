# MasterClock Lite

**A browser-based synchronized clock for studios, control rooms and live production environments.**

<img width="1933" height="1080" alt="image" src="https://github.com/user-attachments/assets/8b02f8fe-7903-4321-a847-2c54c9b3c98d" />

MasterClock Lite is a lightweight, standalone HTML clock designed for environments where a clear and reliable time reference is essential.

Unlike a conventional clock that simply displays the computer's system time, MasterClock Lite synchronizes its internal time reference with external UTC time services. It estimates the difference between the local device clock and external sources, then uses that offset alongside the browser's monotonic clock to maintain a continuous, accurate display.

The application runs entirely in the browser in a single HTML file, without requiring a dedicated server, backend, build step, or additional software installation.

## Features

- **External Time Synchronization** – Retrieves UTC time from external services and calculates the offset relative to the local device clock.
- **Multi-Source Verification** – Queries independent time services to compare synchronization results and verify agreement.
- **HTTP Date Fallback** – If the primary time APIs are unreachable, the application falls back to sampling the HTTP `Date` response header using interval intersection (Marzullo's algorithm).
- **Continuous Monotonic Timekeeping** – Advances synchronized time using `performance.now()`, keeping the display stable even if the system clock changes between sync cycles.
- **Wake & Drift Detection** – Automatically triggers a resynchronization if clock drift is detected after system sleep or background throttling.
- **Automatic & Manual Resynchronization** – Periodically refreshes the time reference in the background and allows manual resync at any time.
- **Millisecond Precision** – Displays real-time milliseconds alongside standard hours, minutes, and seconds.
- **Synchronization Telemetry** – Reports clock offset, estimated uncertainty, network round-trip time (RTT), active source, and lock/holdover status.
- **Multi-Zone & World Time Support** – Supports UTC, local device time, standard IANA time zones, and simultaneous monitoring of five configurable global time hubs with relative offsets.
- **Multilingual Interface** – Automatically detects browser language and supports 11 languages (`EN`, `ES`, `FR`, `DE`, `IT`, `PL`, `NO`, `DA`, `RU`, `ZH`, `RO`).
- **Responsive & Persistent** – Adapts cleanly to control room monitors, desktops, and mobile screens, saving all preferences locally via `localStorage`.

## Time Synchronization

MasterClock Lite uses a lightweight, client-side synchronization mechanism:

1. Sends multiple requests to external UTC time services, measuring round-trip timing via `performance.now()`.
2. Selects the sample with the lowest estimated uncertainty (`RTT / 2 + 1 ms`).
3. Cross-checks independent sources when multiple services respond to verify consistency.
4. Computes an offset between true UTC and the browser's monotonic clock.
5. Maintains the displayed time independently of the operating system clock.

When external APIs are unavailable, the application can derive time from repeated HTTP `Date` headers using interval consensus. If all network sources become temporarily unreachable after an initial lock, the clock enters **holdover** mode and continues running from its last valid offset.

## Synchronization Status

The status bar provides real-time diagnostic feedback:

- **Locked / Synchronized** – A valid external time reference has been acquired within normal uncertainty thresholds.
- **Warning** – Synchronization uncertainty is elevated, sources disagree, or the clock is running in holdover mode.
- **Unavailable** – No external time reference could be obtained yet.

Displayed telemetry includes:

- **Offset** – Difference between the external reference and the local device clock (`ms`).
- **Accuracy** – Estimated synchronization uncertainty (`±ms`).
- **RTT** – Minimum measured network round-trip time (`ms`), when available from API sources.

## Configuration

The settings panel allows users to customize:

- Studio / primary timezone (`UTC`, `Device`, or any IANA timezone)
- Five global hub timezones for multi-location monitoring
- Interface language
- 12-hour (`AM / PM`) or 24-hour time format
- Final-ten-second visual tally pulse
- Manual resynchronization

All preferences are stored locally in the browser (`localStorage`) and require no account or external configuration.

## Browser Application

**[Open MasterClock Lite](https://lojewskistudio.github.io/MasterClock-Lite/)**

MasterClock Lite runs directly in your browser. No installation, downloads, or server setup required. Internet access is required for external time synchronization.

## Technical Details

- HTML5 & CSS3 (Container Queries, CSS Custom Properties, Grid/Flexbox)
- Vanilla JavaScript (ES6+)
- Fetch API & `AbortController`
- High Resolution Time API (`performance.now()`, `performance.timeOrigin`)
- Internationalization API (`Intl.DateTimeFormat`, `Intl.supportedValuesOf`)
- `requestAnimationFrame` rendering loop
- Web Storage API (`localStorage`)

Zero external dependencies or frameworks.

## Important Notes

MasterClock Lite is a browser-based time reference, not a certified precision timekeeping instrument (such as a hardware PTP/NTP master clock or GPSDO).

Actual accuracy depends on network latency, path asymmetry, external service availability, browser timer resolution, and the hosting environment. The displayed uncertainty is an estimate derived from network sampling rather than a guaranteed specification.

The application does not modify the operating system clock; it maintains an independent time reference inside the browser tab. For mission-critical broadcast or playout operations, independent verification against a primary station reference is recommended.

## License

MIT License.

Copyright (c) 2026 Mihai Lucian Lojewski / Lojewski Studio.

See [LICENSE](LICENSE) for details.

---

**MasterClock Lite**

*An independent time reference, directly in your browser.*

Lojewski Studio · 2026
