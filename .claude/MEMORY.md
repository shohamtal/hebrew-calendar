# Hebrew Calendar Reminders - Project Memory

## Overview
Client-side web app for adding Hebrew calendar events directly to Google Calendar.
- **Live:** https://shohamtal.github.io/hebrew-calendar/
- **Repo:** https://github.com/shohamtal/hebrew-calendar
- **Single file:** `index.html` (no build, no server)

## Features
1. **Sefirat HaOmer** - 49 daily reminders for any of the next 10 years
2. **Hebrew Date Reminders** - recurring events by Hebrew date (birthdays, yahrzeits, etc.)
3. **Preview step** - shows table of all events before creating them
4. Both features create events directly in Google Calendar via API

## Architecture
- **Hosting:** GitHub Pages (static)
- **Hebrew calendar:** `@hebcal/core` v5 loaded from `esm.sh` CDN
- **Auth:** Google Identity Services (GSI) - OAuth popup flow
- **Calendar API:** Direct REST calls (`POST /calendars/primary/events`) from browser
- **Client ID:** `397711694217-t007f4ecm3h81tv9k0cs35urvvuupt0t.apps.googleusercontent.com` (Web Application type, hardcoded - not sensitive)

## Key Technical Decisions
- **Omer start date:** Uses `HDate(15, 'Nisan', hebrewYear)` because events are at 20:00 (after sunset), so evening of 15 Nisan = halachic start of 16 Nisan = Day 1
- **Hebrew year calculation:** Nisan-Elul → `gregorianYear + 3760`, Tishrei-Adar → `gregorianYear + 3761`
- **Nusach:** "בעומר" (not "לעומר"), plus prayer "הרחמן הוא יחזיר לנו עבודת בית המקדש למקומה במהרה בימינו אמן סלה"
- **Day selector:** Uses Hebrew letters (א׳, ב׳, ... ט״ו, ט״ז, ... ל׳)
- **Rate limiting:** Small delay every 10 events to avoid Google API throttling

## Google Cloud Setup
- Project ID: `1098850984715`
- Google Calendar API must be enabled in the project
- OAuth Client must have `https://shohamtal.github.io` in Authorized JavaScript origins
- Also add `http://localhost:PORT` for local development
- Scope: `https://www.googleapis.com/auth/calendar.events`

## Past Issues & Fixes
- Initial version used `HDate(16, 'Nisan')` which was off by one day (events at 20:00 are already after sunset)
- Changed "לעומר" to "בעומר" per correct nusach
- Removed setup screen - Client ID is hardcoded (it's public, not sensitive)
- Added preview table before creating events so user can verify dates
