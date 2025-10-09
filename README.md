# Cordova Calendar Plugin 5.1.8-dev

This is a fork of the [Calendar-PhoneGap-Plugin](https://github.com/EddyVerbruggen/Calendar-PhoneGap-Plugin) by [EddyVerbruggen](https://github.com/EddyVerbruggen), which will continue the support of this plugin to the community.

If you like, you can support me on my [GitHub Sponsor page](https://github.com/sponsors/GitToTheHub).

1. [Description](#1-description)
2. [Installation](#2-installation)
3. [Usage](#3-usage)
4. [Promises](#4-promises)
5. [Credits](#5-credits)
6. [License](#6-license)

## 1. Description

This plugin allows you to add events to the Calendar of the mobile device.

### iOS specifics
* Supported methods: `find`, `create`, `modify`, `delete`, ..
* All methods work without showing the native calendar. Your app never loses control.
* Tested on iOS 6+.

#### Calendar usage description
You need to provide a reason to the user for Calendar access. This plugin adds the key `NSCalendarsUsageDescription` to your App's Info.plist with the default text `This app uses your calendar`, which you can override with the preference key `CALENDAR_USAGE_DESCRIPTION`. To do so, pass the following variable when installing the plugin:

```bash
cordova plugin add https://github.com/GitToTheHub/cordova-plugin-calendar-2 --variable CALENDAR_USAGE_DESCRIPTION="My custom calendar usage reason"
```

#### Contacts usage description
You need to provide a reason to the user for contacts access. This is needed in some iOS versions when searching for locations and invitees using the interactive mode like `createEventInteractively`. This plugin adds the key `NSContactsUsageDescription` to your App's Info.plist with the defaul text `This app uses contacts when searching for locations and invitees when using the calendar functionality`, which you can override with the preference key `CONTACTS_USAGE_DESCRIPTION`. To do so, pass the following variable when installing the plugin:

```bash
cordova plugin add https://github.com/GitToTheHub/cordova-plugin-calendar-2 --variable CONTACTS_USAGE_DESCRIPTION="My custom contacts usage reason"
```

### Android specifics
* Supported methods on Android 4: `find`, `create` (silent and interactive), `delete`, ..

## 2. Installation

Latest version from GitHub:

```
$ cordova plugin add https://github.com/GitToTheHub/cordova-plugin-calendar-2
```

## 3. Usage

The table gives an overview of basic operation compatibility:

Operation                           | Comment     | iOS | Android |
----------------------------------- | ----------- | --- | ------- |
createCalendar                      |             | yes | yes     |
deleteCalendar                      |             | yes | yes     |
createEvent                         | silent      | yes | yes     |
createEventWithOptions              | silent      | yes | yes     |
createEventInteractively            | interactive | yes | yes     |
createEventInteractivelyWithOptions | interactive | yes | yes     |
findEvent                           |             | yes | yes     |
findEventWithOptions                |             | yes | yes     |
listEventsInRange                   |             | yes | yes     |
listCalendars                       |             | yes | yes     |
findAllEventsInNamedCalendars       |             | yes |         |
modifyEvent                         |             | yes |         |
modifyEventWithOptions              |             | yes |         |
deleteEvent                         |             | yes | yes     |
deleteEventFromNamedCalendar        |             | yes |         |
deleteEventById                     |             | yes | yes     |
openCalendar                        |             | yes | yes     |

## Examples

```js
  // Common successCallback for all examples
  const success = (response) => {
    alert("Success: " + JSON.stringify(response))
  }

  // Common errorCallback for all examples
  const error = (message) => {
    alert("Error: " + message)
  }

  // Create a calendar
  // Under Android a local calendar will be created in a local account and not in the Google account.
  // So it will not be synchronized.
  // Option 1: Set the calendar name directly
  window.plugins.calendar.createCalendar(calendarName, success, error);

  // Option 2: Set options for creating the calendar
  let createCalOptions = window.plugins.calendar.getCreateCalendarOptions();

  // Android only. Sets the local account name where the local calendar is created under.
  // If you don't set it, the app name will be used as account name.
  createCalOptions.accountName = "My Account name";

  createCalOptions.calendarName = "My Cal Name";

  // Optional, set the calendar color by a hex color like `#FF0000"`, default is null, so the OS picks a color.
  createCalOptions.calendarColor = "#FF0000";

  // Create the calendar with options
  window.plugins.calendar.createCalendar(createCalOptions, success, error);

  // delete a calendar
  window.plugins.calendar.deleteCalendar(calendarName, success, error);

  // Prepare some variables for event creation
  // Beware: First Month = 0, Last Month = 11
  const startDate = new Date(2015, 2, 15, 18, 30, 0, 0, 0);
  const endDate = new Date(2015, 2, 15, 19, 30, 0, 0, 0);
  const title = "My nice event";
  const eventLocation = "Home";
  const notes = "Some notes about this event.";

  // create an event silently
  window.plugins.calendar.createEvent(title, eventLocation, notes, startDate, endDate, success, error);

  // create an event silently with options:
  var calOptions = window.plugins.calendar.getCalendarOptions(); // grab the defaults
  calOptions.firstReminderMinutes = 120; // default is 60, pass in null for no reminder (alarm)
  calOptions.secondReminderMinutes = 5;
  calOptions.recurrence = "monthly"; // supported are: daily, weekly, monthly, yearly
  calOptions.recurrenceEndDate = new Date(2016,10,1,0,0,0,0,0); // leave null to add events into infinity and beyond
  calOptions.calendarName = "MyCreatedCalendar"; // iOS only
  calOptions.calendarId = 1; // Android only, use id obtained from listCalendars() call which is described below. This will be ignored on iOS in favor of calendarName and vice versa. Default: 1.
  calOptions.recurrenceInterval = 2; // once every 2 months in this case, default: 1

  // And the URL can be passed (will be appended to the notes on Android as there doesn't seem to be a sep field)
  calOptions.url = "https://www.google.com";

  // on iOS the success handler receives the event ID
  window.plugins.calendar.createEventWithOptions(title, eventLocation, notes, startDate, endDate, calOptions, success, error);

  // create an event interactively
  window.plugins.calendar.createEventInteractively(title, eventLocation, notes, startDate, endDate, success, error);

  // create an event interactively with the calOptions object as shown above
  window.plugins.calendar.createEventInteractivelyWithOptions(title, eventLocation, notes, startDate, endDate, calOptions, success, error);

  // create an event in a named calendar (iOS only, deprecated, use createEventWithOptions instead)
  window.plugins.calendar.createEventInNamedCalendar(title, eventLocation, notes, startDate, endDate, calendarName, success, error);

  // find events (on iOS this includes a list of attendees (if any))
  window.plugins.calendar.findEvent(title, eventLocation, notes, startDate, endDate, success, error);

  // if you need to find events in a specific calendar, use this one. All options are currently ignored when finding events, except for the calendarName.
  var calOptions = window.plugins.calendar.getCalendarOptions();
  calOptions.calendarName = "MyCreatedCalendar"; // iOS only
  calOptions.id = "D9B1D85E-1182-458D-B110-4425F17819F1"; // if not found, we try matching against title, etc
  window.plugins.calendar.findEventWithOptions(title, eventLocation, notes, startDate, endDate, calOptions, success, error);

  // list all events in a date range (only supported on Android for now)
  window.plugins.calendar.listEventsInRange(startDate, endDate, success, error);

  // list all calendar names - returns this JS Object to the success callback: [{"id":"1", "name":"first"}, ..]
  window.plugins.calendar.listCalendars(success, error);

  // find all _future_ events in the first calendar with the specified name (iOS only for now, this includes a list of attendees (if any))
  window.plugins.calendar.findAllEventsInNamedCalendar(calendarName, success, error);

  // change an event (iOS only for now)
  var newTitle = "New title!";
  window.plugins.calendar.modifyEvent(title, eventLocation notes, startDate, endDate, newTitle, eventLocation, notes, startDate, endDate, success, error);

  // or to add a reminder, make it recurring, change the calendar, or the url, use this one:
  var filterOptions = window.plugins.calendar.getCalendarOptions(); // or {} or null for the defaults
  filterOptions.calendarName = "Bla"; // iOS only
  filterOptions.id = "D9B1D85E-1182-458D-B110-4425F17819F1"; // iOS only, get it from createEventWithOptions (if not found, we try matching against title, etc)
  var newOptions = window.plugins.calendar.getCalendarOptions();
  newOptions.calendaName = "New Bla"; // make sure this calendar exists before moving the event to it
  // not passing in reminders will wipe them from the event. To wipe the default first reminder (60), set it to null.
  newOptions.firstReminderMinutes = 120;
  window.plugins.calendar.modifyEventWithOptions(title, eventLocation, notes, startDate, endDate, newTitle, eventLocation, notes, startDate, endDate, filterOptions, newOptions, success, error);

  // delete an event (you can pass nulls for irrelevant parameters). The dates are mandatory and represent a date range to delete events in.
  // note that on iOS there is a bug where the timespan must not be larger than 4 years, see issue 102 for details.. call this method multiple times if need be
  // You can match events starting with a prefix title, so if your event title is 'My app - cool event' then 'My app -' will match.
  window.plugins.calendar.deleteEvent(newTitle, eventLocation, notes, startDate, endDate, success, error);

  // delete an event, as above, but for a specific calendar (iOS only)
  window.plugins.calendar.deleteEventFromNamedCalendar(newTitle, eventLocation, notes, startDate, endDate, calendarName, success, error);

  // delete an event by id. If the event has recurring instances, all will be deleted unless `fromDate` is specified, which will delete from that date onward. (iOS and android only)
  window.plugins.calendar.deleteEventById(id, fromDate, success, error);

  // open the calendar app:
  // - open it at 'today'
  window.plugins.calendar.openCalendar();

  // - open at a specific date, here today + 3 days
  var d = new Date(new Date().getTime() + 3 * 24 * 60 * 60 * 1000);
  window.plugins.calendar.openCalendar(d, success, error); // callbacks are optional
```

Creating an all day event:
```js
  // set the startdate to midnight and set the enddate to midnight the next day
  var startDate = new Date(2014,2,15,0,0,0,0,0);
  var endDate = new Date(2014,2,16,0,0,0,0,0);
```

Creating an event for 3 full days
```js
  // set the startdate to midnight and set the enddate to midnight 3 days later
  var startDate = new Date(2014,2,24,0,0,0,0,0);
  var endDate = new Date(2014,2,27,0,0,0,0,0);
```

Example Response IOS getCalendarOptions
```js
{
calendarId: null,
calendarName: "calendar",
firstReminderMinutes: 60,
recurrence: null,
recurrenceEndDate: null,
recurrenceInterval: 1,
secondReminderMinutes: null,
url: null
}
```

Exmaple Response IOS Calendars
```js
{
id: "258B0D99-394C-4189-9250-9488F75B399D",
name: "standard calendar",
type: "Local"
}
```

Exmaple Response IOS Event
```js
{
calendar: "Kalender",
endDate: "2016-06-10 23:59:59",
id: "0F9990EB-05A7-40DB-B082-424A85B59F90",
lastModifiedDate: "2016-06-13 09:14:02",
location: "",
message: "my description",
startDate: "2016-06-10 00:00:00",
title: "myEvent"
}
```

### Android 6 (M) Permissions
On Android 6 you need to request permission to use the Calendar at runtime when targeting API level 23+.
Even if the `uses-permission` tags for the Calendar are present in `AndroidManifest.xml`.

Since plugin version 4.5.0 we transparently handle this for you in a just-in-time manner.
So if you call `createEvent` we will pop up the permission dialog. After the user granted access
to his calendar the event will be created.

You can also manually manage and check permissions if that's your thing.
Note that the hasPermission functions will return true when:

- You're running this on iOS, or
- You're targeting an API level lower than 23, or
- You're using Android < 6, or
- You've already granted permission.

```js
  // again, this is no longer needed with plugin version 4.5.0 and up
  function hasReadWritePermission() {
    window.plugins.calendar.hasReadWritePermission(
      function(result) {
        // if this is 'false' you probably want to call 'requestReadWritePermission' now
        alert(result);
      }
    )
  }

  function requestReadWritePermission() {
    // no callbacks required as this opens a popup which returns async
    window.plugins.calendar.requestReadWritePermission();
  }
```

There are similar methods for Read and Write access only (`hasReadPermission`, etc),
although it looks like that if you request read permission you can write as well,
so you might as well stick with the example above.

Note that backward compatibility was added by checking for read or write permission in the relevant plugins functions.
If permission is needed the plugin will now show the permission request popup.
The user will then need to allow access and invoke the same method again after doing so.

## 4. Promises
If you like to use promises instead of callbacks, or struggle to create a lot of
events asynchronously with this plugin then I encourage you to take a look at
[this awesome wrapper](https://github.com/poetic-labs/native-calender-api) for
this plugin. Kudos to [John Rodney](https://github.com/JohnRodney) for this piece of art!

## 5. Credits

This plugin was enhanced for Plugman / PhoneGap Build by [Eddy Verbruggen](http://www.x-services.nl). I fixed some issues in the native code (mainly for iOS) and changed the JS-Native functions a little in order to make a universal JS API for both platforms.
* Inspired by [this nice blog of Devgirl](http://devgirl.org/2013/07/17/tutorial-how-to-write-a-phonegap-plugin-for-android/).
* Credits for the original iOS code go to [Felix Montanez](https://github.com/felixactv8/Phonegap-Calendar-Plugin-ios).
* Credits for the original Android code go to [Ten Forward Consulting](https://github.com/tenforwardconsulting/Phonegap-Calendar-Plugin-android) and [twistandshout](https://github.com/twistandshout/phonegap-calendar-plugin).
* Special thanks to [four32c.com](http://four32c.com) for sponsoring part of the implementation, while keeping the plugin opensource.

## 6. License

[The MIT License (MIT)](http://www.opensource.org/licenses/mit-license.html)

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in
all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
THE SOFTWARE.
