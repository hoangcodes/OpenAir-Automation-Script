# OpenAir-Automation-Script

## About

This repo contains a script to help automate time tracking that professionals have to do on a weekly basis. Specifically for SuiteProjects : OpenAir

## Get Started

1. Download the excel file
2. Open OpenAir on Chrome (or your browser of choice) and create a new Timesheet
3. **Manually** fill out ALL the Client : Engagement column for your timesheet this week (Important: do NOT use the copy icon! The rows do not get created in sequential order, this is documented in known bugs)
4. Open the developer console (Ctrl + Shift + J on Windows, Cmd + Option + J on Mac)
5. Copy code from excel file and paste to the console (remember to remove the '' around the code if you are copy and pasting from excel)

### Data and Script

Parameters
| Parameter | Type    | Description                                                                                   | Constraints       | 
| --------- | ------- | --------------------------------------------------------------------------------------------- | ----------------- |
| row       | integer | Row number in OpenAir, 1 is the first row, 2 is the second row, and so on..                   | none              | 
| col       | integer | Column number in OpenAir, In OpenAir, 3=Sunday, 4=Monday, 5=Tuesday.. 9=Saturday              | value must be 3-9 |
| hours     | float   | Number of hours you want to input. In excel it automatically stores numbers as floats         | none              |
| notes     | string  | Any comments the user wants to input into the comment section                                 | none              |


### Data in JSON format (this should change)

The data structure MUST be in this JSON format
```json
const data = [
  {row: 1, col: 3, hours: 4.0, notes: '[0000] EFT Bill Payment'},
  {row: 2, col: 3, hours: 4.0, notes: '[0001] Roles & Permissions'},
  ...
]
```

### Script that takes your timesheetData (from previous step) and fills out the information in OpenAir (this script does not change)
```json
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

### Known Bugs

Clicking the Copy icon

On the front-end, when a user clicks the copy icon, on the back-end the rows are not being created in sequential order. 

For example, if you input Client : Engagement normally this will create rows in sequential order 1, 2, 3, 4, 5...

If you use the copy icon, it will not create rows in sequential order. For example, 1, 5, 4, 3, 2... You see how 3, 4, and 5 got created in between 1 and 2 and 2 continued to get pushed down the list.

Solution: Recommend the user not to click the copy icon and to only create new rows via the Client : Engagement dropdown. The new roles get created automatically after the user inputs a Client : Engagement.

### Languages and Tools:

<div align='left' width=100% margin-bottom:2px>
  <img src="https://img.shields.io/badge/JavaScript-F7DF1E.svg?style=for-the-badge&logo=JavaScript&logoColor=black" />
</div>
