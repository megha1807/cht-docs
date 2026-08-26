---
title: "Bikram Sambat Datepicker"
linkTitle: "Bikram Sambat Datepicker"
weight: 15
description: >
  How to configure and use the Bikram Sambat (Nepali calendar) datepicker in CHT forms and filters.
relatedContent: >
  building/forms/app
  building/forms/configuring/form-inputs
---

The CHT supports the **Bikram Sambat (BS)** calendar natively, allowing community health workers and supervisors in Nepal to record dates and filter reports using their official calendar system. 

The calendar UI is powered by the `bikram-sambat-calendar` package, which delegates all date conversions, calculations, and month lengths to the core `bikram-sambat` library. This ensures that dates are never corrupted or misaligned across month boundaries (e.g. handling variable month lengths of 29 to 32 days).

---

## 📍 Where the Datepicker Appears

The Bikram Sambat datepicker is automatically active in two primary locations of the CHT application:

### 1. Enketo Web Forms
When a user is filling out a form (e.g., registering a pregnancy, recording a delivery, or scheduling a visit), any `date` question type will automatically display the Bikram Sambat calendar grid dropdown when the user's language is set to **Nepali (`ne`)**.

* **Under the Hood:** The datepicker displays the Nepali calendar grid to the user, but standardizes the output to a Gregorian ISO date (e.g. `2026-08-20`) when saving the form. This allows targets, tasks, and SMS reminders to compile correctly using standard date libraries.

### 2. Reports Page date filter
Supervisors viewing submitted reports can filter reports by dates. In the filter sidebar, clicking the "From" (बाट) or "To" (सम्म) date input fields opens the Bikram Sambat calendar grid, allowing them to filter reports by specific Nepali months.

---

## 🛠️ XLSForm Configuration

To use the datepicker in your XLSForms, simply configure a standard `date` question:

| type | name | label | hint |
| :--- | :--- | :--- | :--- |
| `date` | `date_of_delivery` | सुत्केरी भएको मिति | सुत्केरी भएको मिति छान्नुहोस् |

### Automatic Language Activation
By default, Enketo widgets check the user's active language. If the language is set to Nepali (`ne`), Enketo replaces the default Gregorian HTML5 datepicker with the custom Bikram Sambat calendar grid.

### Working with the Date in XPath
If you need to display the selected date back to the user in a summary note or calculation using the Bikram Sambat format, use the CHT-specific `to-bikram-sambat` XPath function:

| type | name | label | calculation |
| :--- | :--- | :--- | :--- |
| `calculate` | `bs_delivery_date` | | `to-bikram-sambat(${date_of_delivery})` |
| `note` | `summary` | Delivery date was: `${bs_delivery_date}` | |

---

## ⚙️ Programmatic JavaScript API (For Developers)

If you are writing custom extension libraries or modifying CHT Core, you can import and initialize the datepicker directly:

```javascript
import $ from 'jquery';
import 'bikram-sambat-calendar';

// Initialize the datepicker on a standard text input
$('#date-input').nepaliDatePicker({
  ndpTriggerButton: true,
  ndpYear: true,
  ndpMonth: true,
  ndpYearCount: 20,
  readOnlyInput: true
});
```

### Handy Helper Functions

Developers can leverage the underlying `calendarFunctions` library for manual date conversions:

```javascript
import { calendarFunctions } from 'bikram-sambat-calendar';

// 1. Convert BS Date to Gregorian Date Object
const adDate = calendarFunctions.getAdDateByBsDate(2083, 4, 25); // AD: 2026-08-10

// 2. Convert Gregorian Date to BS Object
const bsDate = calendarFunctions.getBsDateByAdDate(2026, 8, 10); // BS: 2083-04-25

// 3. Get exact days in a specific Nepali month (ranges from 29 to 32)
const monthDays = calendarFunctions.getBsMonthDays(2082, 8); // Returns 29
```
