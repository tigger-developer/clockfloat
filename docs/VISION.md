---
title: Clockfloat Vision
version: 1
last-updated: 2026-10-16
---

# Clockfloat Vision

Clockfloat lets people reclaim screen space by hiding the macOS menu bar and
Dock without losing a glanceable clock. It keeps the current time and date
visible above other applications.

## Intended users and context

Clockfloat is for Mac users who prefer to hide the menu bar and Dock but still
want to see the time and date while working in full-screen or overlapping
applications. The need comes from a simple trade-off: hiding system interface
bars makes more room for work, while hiding the menu bar also removes its
built-in clock.

## Outcomes

- The time and date remain easy to glance at while other applications are in
  front.
- The display can be placed around the screen and adjusted to suit its visual
  surroundings.
- The clock remains useful across connected displays and responds to ordinary
  screen changes.
- Users can choose how the display behaves when the pointer reaches it.

## Product scope

Clockfloat is a small native macOS utility centred on a floating time and date
display. Its settings cover placement, typography, formats, opacity, late
offset, and pointer behaviour. Its purpose is to preserve access to time while
keeping the menu bar and Dock hidden; it is not a replacement for those system
surfaces.

## Direction

Product decisions should preserve the balance between reclaimed screen space
and glanceable time. The clock should remain readable, unobtrusive, and
configurable without becoming a general desktop dashboard. New capabilities
should support that purpose directly.

## Changelog

- 1 (2026-10-16): Initial vision reverse-engineered from the project overview
  and current application behaviour.
