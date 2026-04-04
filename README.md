## OpenAir Timesheet Helper

## About

This repo contains a step-by-step guide to help professionals automate weekly time tracking, gathering data from Excel to SuiteProjects : OpenAir. This automation uses Bookmarklet, a small JavaScript application stored as a browser bookmark.

### Getting started (step-by-step guide)

1. Download the excel template on this website (repo), click the file above `Timesheet template v1.xlsx` and click `Download raw file` icon

![Download template](https://github.com/user-attachments/assets/0b594ca3-6b7a-4484-a0fd-53b21aefd040)

2. Open OpenAir and fill out the **Client : Engagement** and **Task** for the week. You need to help expose the rows because the script can't brute force expose it. Currently, the script can only fill in **Time** and **Notes** information.

![New Timesheet in OpenAir](https://github.com/user-attachments/assets/5c284d28-f07b-47db-9fb5-1d65a09d1b2a)

3. Complete your timesheet data for the week and visit <a href="https://hoangcodes.github.io/openair-timesheet-helper/" target="_blank">https://hoangcodes.github.io/openair-timesheet-helper/</a> and follow the step-by-step guide there

![Timesheet Website](https://github.com/user-attachments/assets/def34f63-15ca-48c6-8b61-4637cbf29f56)

## Data and Script

### Data

The data structure MUST be in this format:

```js
const data = [
  {row: 1, col: 3, hours: 4.0, notes: '[0000] EFT Bill Payment'},
  {row: 2, col: 3, hours: 4.0, notes: '[0001] Roles & Permissions'},
  ...
]
```

### Script that takes timesheet data and fills out the information in OpenAir

```js
// Function that invokes a Promise, which is an async process (because Javascript is synchronous)
function delay(ms) {
  return new Promise((resolve) => setTimeout(resolve, ms));
}

// Function to take the data and input it into OpenAir website
async function fillTimesheet() {
  for (const entry of data) {
    const inputId = `ts_c${entry.col}_r${entry.row}`;
    const notesId = `ts_notes_c${entry.col}_r${entry.row}`;

    // Set hours
    const input = document.getElementById(inputId);
    if (input) {
      input.value = entry.hours;
      input.dispatchEvent(new Event('input', { bubbles: true }));
      input.dispatchEvent(new Event('change', { bubbles: true }));
    }

    // Set notes if present. The delay is important because if too fast the comment won't input properly in the UI
    if (entry.notes) {
      await delay(200);
      document.getElementById(notesId).click();
      await delay(350);

      const textarea = document.getElementById('tm_notes');
      if (textarea) {
        textarea.value = entry.notes;
        textarea.dispatchEvent(new Event('input', { bubbles: true }));
        textarea.dispatchEvent(new Event('change', { bubbles: true }));
      }

      await delay(200);
      document.querySelector('.dialogOkButton').click();
      await delay(200);
    }
  }

  console.log('✅ Timesheet filled successfully!');
}

fillTimesheet();
```

### Parameters

| Parameter | Type    | Description                                                                           | Constraints       |
| --------- | ------- | ------------------------------------------------------------------------------------- | ----------------- |
| row       | integer | Row number in OpenAir, 1 is the first row, 2 is the second row, and so on..           | none              |
| col       | integer | Column number in OpenAir, In OpenAir, 3=Sunday, 4=Monday, 5=Tuesday.. 9=Saturday      | value must be 3-9 |
| hours     | float   | Number of hours you want to input. In excel it automatically stores numbers as floats | none              |
| notes     | string  | Any comments the user wants to input into the comment section                         | none              |

## Future additions

> Fill out the Client : Engagement and Task data

## Known Bugs

### Clicking the Copy icon

> On the front-end, when a user clicks the copy icon, on the back-end the rows are not being created in sequential order.

> For example, if you input Client : Engagement normally this will create rows in sequential order 1, 2, 3, 4, 5...

> If you use the copy icon, it will not create rows in sequential order. For example, 1, 5, 4, 3, 2... You see how 3, 4, and 5 got created in between 1 and 2 and 2 continued to get pushed down the list.

> Solution: Recommend the user not to click the copy icon and to only create new rows via the Client : Engagement dropdown. The new roles get created automatically after the user inputs a Client : Engagement.

### Timesheet is end of the month

> You are going to need to create two separate timesheets for 1 week. So if Sunday-Tuesday is March, and Wednesday-Saturday is April you will create two timesheets, two CSVs, and run the script twice. I know, quite tedious. But if it helps, I think we all dislike how OpenAir is designed.

### Languages and Tools:

<div align='left' width=100% margin-bottom:2px>
  <img src="https://img.shields.io/badge/JavaScript-F7DF1E.svg?style=for-the-badge&logo=JavaScript&logoColor=black" />
</div>
