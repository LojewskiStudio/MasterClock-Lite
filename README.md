# MasterClock Lite

**A browser-based synchronized clock for studios, control rooms and live production environments.**

<img width="1920" height="1080" alt="MasterClock Lite" src="https://github.com/user-attachments/assets/40874a4f-273a-42bf-bd37-e183e5c74ceb" />

MasterClock Lite is a lightweight, standalone HTML clock designed for environments where a clear and reliable time reference is essential.

Unlike a conventional clock that simply displays the computer's system time, MasterClock Lite attempts to synchronize its internal time reference with external UTC time services. It estimates the difference between the device clock and external time sources, then uses that offset to maintain a continuously updated display.

The application runs entirely in the browser, without requiring a dedicated server, backend or additional software installation.

## Features

- **External Time Synchronization** – Retrieves UTC time from external services and calculates the offset relative to the local device clock.
- **Multiple Time Sources** – Uses independent time services to compare synchronization results and assess their agreement.
- **HTTP Date Fallback** – If the primary time services are unavailable, the application can attempt synchronization using the HTTP `Date` response header of the hosting server.
- **Continuous Timekeeping** – Uses the browser's monotonic performance clock to advance synchronized time between synchronization cycles.
- **Automatic Resynchronization** – Periodically checks the external time reference and updates synchronization status.
- **Manual Resynchronization** – Allows the user to initiate a new synchronization at any time.
- **Precision Display** – Displays milliseconds alongside the standard clock.
- **Synchronization Monitoring** – Provides information about clock offset, estimated uncertainty, network round-trip time (RTT) and synchronization status.
- **Timezone Selection** – Supports UTC and available system time zones.
- **Multilingual Interface** – Includes localized interface labels and status messages.
- **Responsive Interface** – Adapts to different screen sizes and display environments.
- **Persistent Settings** – Saves user preferences locally in the browser using `localStorage`.

## Time Synchronization

MasterClock Lite uses a lightweight, client-side synchronization mechanism.

The synchronization process includes:

1. Requests UTC time from external time services.
2. Measures request duration using `performance.now()`.
3. Takes multiple samples and selects the sample with the lowest estimated uncertainty.
4. Compares independent sources when more than one is available.
5. Calculates an offset between external time and the local monotonic clock.
6. Uses the calculated offset to maintain the displayed time independently of subsequent changes to the computer's system clock.

When the primary services cannot be reached, the application can attempt an alternative synchronization method using the HTTP `Date` response header.

If synchronization is temporarily unavailable, the clock continues using its last valid reference (holdover), provided that an earlier synchronization was successful.

## Synchronization Status

The status indicator provides useful diagnostic information:

- **Synchronized** – A valid external time reference has been obtained.
- **Warning** – Synchronization quality is reduced or external sources disagree.
- **Holdover** – The application continues using the last successful synchronization.
- **Unavailable** – No valid time reference could be obtained.

Additional metrics include:

- Clock offset relative to the device clock.
- Estimated synchronization uncertainty.
- Network round-trip time (RTT), when available.
- External time source information.

## Configuration

The settings panel allows users to customize:

- Display appearance.
- Color theme.
- Interface language.
- Timezone.
- 12-hour or 24-hour time format.
- Final-ten-second visual pulse.
- Manual synchronization.

Preferences are stored locally in the browser and do not require an account.

## ## Access the Clock

**[Open MasterClock Lite](https://lojewskistudio.github.io/MasterClock-Lite/)**

MasterClock Lite runs directly in your browser. No installation, downloads or additional configuration required.

Internet access is required for external time synchronization.

## Technical Details

- HTML5
- CSS3
- Vanilla JavaScript
- Browser Fetch API
- Performance API (`performance.now()`)
- Internationalization API (`Intl.DateTimeFormat`)
- Canvas API
- Local Storage

No external JavaScript libraries or frameworks are required.

## Important Notes

MasterClock Lite is a browser-based time reference, not a certified precision timekeeping instrument.

Its actual accuracy depends on network latency, external service availability, server response timestamps, browser execution and the hosting environment.

The displayed synchronization uncertainty is an estimate based on the sampling method, not a guaranteed accuracy specification.

The application does not modify or synchronize the operating system clock. It maintains its own adjusted time reference within the browser.

For professional broadcast or mission-critical timing applications, independent verification against a trusted reference is recommended.

## License

MIT License.

Copyright (c) 2026 Mihai Lucian Lojewski – Lojewski Studio.

See [LICENSE](LICENSE) for details.

---

**MasterClock Lite**

*An independent time reference, directly in your browser.*

Lojewski Studio · 2026
