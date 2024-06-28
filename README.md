# calendar

## how to use

import CalendarComponent.vue

### normal

`
<CalendarComponent
        :date="new Date()"
        :width="400"
        :height="200"
      ></CalendarComponent>`

### date picker

`<CalendarComponent
:date="new Date()"
type="pick"
@pick-date="pickDate"
:width="300"
:height="300" ></CalendarComponent>`

`pickDate(date) {
this.date = date;
}`

### date range picker

`
<CalendarComponent
:date="new Date()"
type="range"
@pick-date-range="pickDateRange"
:width="300"
:height="300" ></CalendarComponent>`

`
pickDateRange(dateRange) {
this.dateRange = dateRange;
}`
