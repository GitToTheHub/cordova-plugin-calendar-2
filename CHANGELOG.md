# Changelog

## Version 5.1.9 (25.04.2026)

- Release for NPM
- No additional changes, just some small changes on the readme

## Version 5.1.8 (12.10.2025)

### Android
- Calendar.js: Add default `accountName` to `getCreateCalendarOptions` and `getCalendarOptions`

### iOS
- Readme: Document `Contacts usage description`
- Fix XCode warnigns:
  - Set defaults for `CALENDAR_USAGE_DESCRIPTION` and `CONTACTS_USAGE_DESCRIPTION` if you didn't set something when adding the plugin.
    - XCode would otherwise complain about an empty calendar or contacts usage description
  - Calendar.m: Use `[UIApplication openURL:options:completionHandler:]` in `openCalendar`.
    - `[UIApplication openURL:]` was used before and is deprecated since iOS 10
  - Calendar.m: Use `self->` for `eventStore` in background thread
    - Fixes warning: `Block implicitly retains 'self'; explicitly mention 'self' to indicate this is intended behavior`

### Common
- Readme: Cleanup formatting and code examples

## Version 5.1.7 (29.06.2025)

### Android
- Account name can be set in the createCalendarOptions. If not set, the app name will used as account name.
  - In the previous version the account name was just "AccountName". If you upgrade to this version and created a calendar with the previous plugin version, the new account name will be used, if you first delete your calendar and create it again.

### iOS
- Updated iOS Plugin to support IOS 17 by #fitmama-nl

### Common
- Removed obsolete Windows platform