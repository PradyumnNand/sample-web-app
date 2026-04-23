# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a single-file HTML application — no build step, no package manager, no server required.

**To run:** Open [application-form.html](application-form.html) directly in a browser.

## Architecture

Everything lives in [application-form.html](application-form.html): styles, markup, and script are all inline.

The app has two views toggled via `display` style:

- `#form-page` — input form with client-side validation (name, email, age, address)
- `#app-page` — read-only confirmation view with a generated reference number (`APP-<base36 timestamp>`) and a print action

Validation logic uses `validateField(id, checkFn)` which toggles `.error` on inputs and `.visible` on `.err-msg` spans. No frameworks, no dependencies.
