---
title: Clockfloat Architecture
version: 1
last-updated: 2026-10-16
---

# Clockfloat Architecture

Clockfloat is a native macOS accessory application. It creates a floating time
and date display for each connected screen, with persisted preferences and a
separate settings window.

## Runtime and platform

The application is written in Swift 5.9 and targets macOS 13. AppKit owns the
application lifecycle and display windows. SwiftUI supplies the settings form,
hosted in an AppKit window. Foundation provides timers, date formatting,
notifications, and `UserDefaults`; Core Graphics identifies displays, and Core
Text registers the bundled White Rabbit font.

The app uses the accessory activation policy, so it runs without a regular Dock
presence. The runtime entry point creates one `Clock` application delegate and
starts the AppKit event loop.

## Components and responsibilities

| Component | Responsibility |
| --- | --- |
| `main.swift` | Creates the application delegate, selects accessory activation, and starts the event loop. |
| `Clock` in `Clock.swift` | Owns configuration in memory, creates and tears down per-display windows, observes display and settings changes, and creates ticking labels. |
| `ClockConfiguration` in `Clock.swift` | Defines settings, defaults, persistence, font validation, and derived values such as late offset and text alpha. |
| `EvasiveWindow` in `EvasiveWindow.swift` | Renders a borderless, floating window; positions it relative to its screen or paired window; handles hover behaviour and opens settings on right-click. |
| `HoverTrackingCellView` in `EvasiveWindow.swift` | Tracks pointer entry and delegates the configured dodge or hide behaviour to its window. |
| `SettingsWindow` and `SettingsView` | Present the SwiftUI preferences form inside an AppKit floating window. `@AppStorage` reads and writes the same `UserDefaults` keys used by `ClockConfiguration`. |
| `TickingTextField` in `TickingTextArea.swift` | Holds and invalidates the repeating timer associated with a displayed label. |
| `VerticallyCenteredTextFieldCell` | Adjusts text drawing to vertically centre the label. |

## Display and update flow

At launch, `Clock` loads preferences, registers the bundled font, then creates a
time window and a date window for each screen with a display identifier. The
time window is placed at the configured corner or centre position. The date
window is paired with the time window and positioned above or below it. Each
label uses a repeating Foundation timer and a `DateFormatter`; time refreshes
every second and date refreshes every ten seconds. The configured late offset
is applied to the date used for both labels.

When display parameters change, `Clock` closes existing pairs and rebuilds them
for the current screens. When a setting changes, the settings view posts the
`clockConfigurationDidChange` notification. `Clock` reloads persisted values
and rebuilds the windows so their appearance and placement reflect the new
configuration. Closing a window invalidates its label and hide timers.

## Configuration and state ownership

`UserDefaults.standard` is the persistent source of truth for user preferences.
`ClockConfiguration` defines the keys and defaults, converts persisted raw
values into typed settings, and supplies fallback values for unknown corner,
hover, or font values. `Clock` holds the current in-memory configuration and
reloads it after settings notifications. The settings form binds directly to
those same preference keys through `@AppStorage`.

The app's display windows, timers, and observers are runtime state owned by
`Clock` and the window/label instances. They are recreated on display or
configuration changes and torn down when the application terminates. The
project profile declares no managed infrastructure. The application code has
no network service boundary; its preferences are local macOS user defaults.

## Window interaction

Each display pair uses borderless windows at the status-bar window level, with
participation across Spaces and full-screen auxiliary contexts. The time window
owns the selected orientation; the paired date window follows it. Hover
behaviour is one of:

- `none`: leave the clock in place and allow the window background to be
  dragged.
- `dodge`: move the pair to the next of six supported positions.
- `hide`: fade the pair out, then restore it after five seconds.

Holding Shift while entering a tracked clock area suppresses the configured
hover action. Right-clicking either display window opens the shared settings
window.

## Requirements and verification

Product requirements and their legacy acceptance criteria are maintained in
the [work ledger](work.org#legacy-acceptance-criteria). The focused unit-test
target exercises `ClockConfiguration` persistence and calculations. It does
not by itself verify AppKit window placement, multi-display behaviour, hover
interaction, or live settings presentation; those require suitable integration
or human validation. The project records historical test limitations and
unresolved legacy evidence in the work ledger.

## Changelog

- 1 (2026-10-16): Initial reverse-engineered architecture description.
