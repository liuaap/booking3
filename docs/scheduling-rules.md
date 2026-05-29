# Scheduling Rules

This document records the scheduling decisions for clinic and doctor availability.

## Core Model

The scheduling system has four editing modes:

1. Clinic fixed mode
2. Clinic temporary mode
3. Doctor fixed mode
4. Doctor temporary mode

Clinic availability is the parent boundary. Doctor availability must be edited inside the clinic's open time.

## Clinic Fixed Mode

Clinic fixed mode defines the clinic's normal weekly business hours.

Example:

```text
Tuesday 09:00-12:00
Saturday closed
```

These hours are the weekly baseline for the clinic.

## Clinic Temporary Mode

Clinic temporary mode defines one-day exceptions.

Examples:

```text
2026-05-26 temporary closed
2026-05-26 temporary open 15:00-18:00
2026-05-26 shortened hours 09:00-10:00
```

Clinic temporary status has higher priority than clinic fixed hours.

If the clinic is closed on a date, all doctors are unavailable on that date, including fixed schedules and temporary doctor schedules.

## Doctor Fixed Mode

Doctor fixed mode defines the doctor's normal weekly working hours at a clinic.

Doctor fixed hours are limited by the clinic fixed hours for the same weekday.

Example:

```text
Clinic Tuesday fixed hours: 09:00-12:00
Doctor selected range: 10:00-12:00
Apply every Tuesday
```

Result:

```text
Doctor fixed Tuesday working hours: 10:00-12:00
```

The clinic time is the allowed boundary. The doctor's selected range is the doctor's actual fixed working time.

Doctor fixed mode is used to update weekly fixed working hours:

```text
Apply every Tuesday
```

One-day or single-slot adjustments can use doctor temporary mode. Temporary mode applies to one day by default, and can also sync the current settings into the doctor's weekly fixed rules.

## Doctor Temporary Mode

Doctor temporary mode defines one-day doctor adjustments.

Doctor temporary mode is also limited by the clinic's effective open time for that date.

Doctor temporary mode applies to one day by default, and can also sync the current settings into the doctor's weekly fixed rules.

Examples:

```text
Doctor temporary extra slots today
Doctor today closed
Doctor today shortened
Sync today's settings into every Wednesday
```

## Time Source Rules

Doctor fixed mode time dropdowns use:

```text
Clinic fixed hours for the selected weekday
```

Doctor temporary mode time dropdowns use:

```text
Clinic effective hours for the selected date
```

Clinic effective hours means:

```text
Clinic temporary mode for the date, if any
otherwise clinic fixed hours for that weekday
```

## Doctor Time Editing Rules

In both doctor fixed mode and doctor temporary mode:

1. Time dropdowns only show clinic-open times.
2. Add, edit, and delete controls must not create doctor times outside clinic-open times.
3. Deleting a doctor time only affects the doctor, not the clinic.
4. Saving must validate again that doctor times are inside clinic-open times.

## Appointment Slot Generation

Appointment slots are generated from doctor working hours and interval minutes.

Example:

```text
Doctor working hours: 10:00-12:00
Interval: 30 minutes
Generated slots: 10:00, 10:30, 11:00, 11:30
```

The whole appointment interval must fit inside the doctor working range.

Example with 30 minutes:

```text
11:30-12:00 valid
12:00-12:30 invalid
```

## Disabled Slots

Doctor working hours and appointment slots are separate concepts.

Example:

```text
Doctor working hours: 10:00-12:00
Disabled slot: 10:30
```

The doctor's working hours remain:

```text
10:00-12:00
```

The available appointment slots are:

```text
10:00, 11:00, 11:30
```

## Valid Appointment Time

A time is valid for appointment booking when all conditions are true:

1. The clinic is not closed on the date.
2. The doctor is not closed on the date.
3. The appointment interval is inside doctor working hours or a valid one-day doctor adjustment.
4. The appointment slot is not disabled by the doctor.
5. The appointment slot is not already booked.
6. The appointment slot is not in the past.
7. The appointment interval fits fully inside the allowed range.

## Priority Order

Use this priority order when resolving availability:

```text
Clinic temporary closed
> Clinic temporary open/shortened hours
> Doctor today closed
> Doctor today temporary adjustment
> Doctor fixed weekly working hours
> Clinic fixed weekly business hours
```

Simplified:

```text
If the clinic is closed, all doctors are closed.
If the clinic is open, doctors can schedule only inside clinic-open time.
Doctor fixed mode updates weekly fixed rules.
Doctor temporary mode can update one-day or single-slot adjustments, and can also sync the current settings into weekly fixed rules.
```

## UI Copy

Suggested labels:

```text
Clinic fixed mode: fixed mode / applies every week
Clinic temporary mode: temporary mode / applies only today
Doctor fixed mode: fixed mode / applies every week
Doctor temporary mode: temporary mode / applies only today / update weekly fixed
```

Doctor pages should use doctor-specific wording:

```text
Doctor working hours
Doctor today closed
Apply every Tuesday
Apply only today
```

Clinic pages should use clinic-specific wording:

```text
Clinic business hours
Today closed
Temporary closed
```

