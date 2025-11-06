# Shellcraft Forms

### The free, open-source, no-code alternative to Typeform.

[![Live Demo](https://img.shields.io/badge/Live-Demo-brightgreen?style=for-the-badge)](https://kaheichan.neocities.org/form) 
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?style=for-the-badge)](https://opensource.org/licenses/MIT)

Shellcraft Forms is a web-based tool that empowers you to create beautiful, multi-step, conversational forms without writing a single line of code. It generates a single, self-contained HTML file that you can host anywhere, with responses sent directly to a private Google Sheet that you view.



##  Key Features

*   **No-Code Interface:** A simple, powerful UI to build your form from start to finish.
*   **Drag & Drop:** Easily reorder questions by dragging them into place.
*   **Live Preview:** See exactly what your form will look like as you build it.
*   **Theme Customization:** Control colors, fonts, and even add a custom background image with a blur effect.
*   **Multiple Question Types:** Includes Short Text, Long Text, Email, Multiple Choice, Yes/No, Rating Scale, and Date fields.
*   **Single File Export:** Generates one html file with everything included. No dependencies, no complex setup.
*   **Google Sheets Backend:** Securely collect all form responses in your own Google Sheet for free.
*   **Fully Responsive:** Forms look great on desktop, tablet, and mobile devices.
*   **Host Anywhere:** Deploy your form on GitHub Pages, Neocities, Netlify, Vercel, or any static web host.
*   **Free & Open Source:** No subscriptions, no limits, no "PRO" features.

## How It Works

The project is split into two main parts:

1.  **The Creator (The html file in this repo):** This is the no-code builder. It's a sophisticated single-page application written in  JavaScript that allows you to configure your form and preview it in real-time. It then compiles all your settings and questions into a new, standalone HTML file.

2.  **The Generated Form (the output file):** This is the final product. It's a completely self-contained HTML file with all the necessary CSS and JavaScript baked in. This file runs the conversational form logic and sends the submission data to the Google Apps Script URL you provide.

The data flow is simple:
`User Fills Out Form` → `JavaScript sends data via POST request` → `Google Apps Script` → `Data is written as a new row in your Google Sheet`

## Getting Started: Creating Your First Form

Follow these four steps to create and deploy your form.

### Step 1: Open the Shellcraft Forms Creator

Navigate to the live creator tool to start building.

**[➡️ Start Building Here](https://kaheichan.neocities.org/form)**

### Step 2: Set Up the Google Sheets Backend

This is a one-time setup to create a private backend that will receive your form data.

1.  **Create a new Google Sheet:** Go to [sheets.new](https://sheets.google.com/create).
2.  **Add a Header:** **This is important!** In cell `A1`, type a header for your first column, like `Timestamp`. The script needs at least one header to work correctly.
3.  **Open Apps Script:** In the menu, go to `Extensions` > `Apps Script`.
4.  **Paste the Code:** Delete any placeholder code and paste the following script:
    ```javascript
    function doPost(e) {
      try {
        var sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
        var headers = sheet.getRange(1, 1, 1, sheet.getLastColumn()).getValues()[0];
        var newRow = new Array(headers.length).fill('');
        
        var headerMap = {};
        headers.forEach(function(header, i) {
          headerMap[header] = i;
        });
        
        for (var key in e.parameter) {
          if (headerMap.hasOwnProperty(key)) {
            newRow[headerMap[key]] = e.parameter[key];
          } else {
            var newHeaderIndex = headers.length;
            sheet.getRange(1, newHeaderIndex + 1).setValue(key);
            headers.push(key);
            headerMap[key] = newHeaderIndex;
            while(newRow.length < headers.length) { newRow.push(''); }
            newRow[newHeaderIndex] = e.parameter[key];
          }
        }
        
        sheet.appendRow(newRow);
        
        return ContentService.createTextOutput(JSON.stringify({ "result": "success" })).setMimeType(ContentService.MimeType.JSON);
      } catch (error) {
        return ContentService.createTextOutput(JSON.stringify({ "result": "error", "error": error.toString() })).setMimeType(ContentService.MimeType.JSON);
      }
    }
    ```
5.  **Deploy as a Web App:**
    *   Click the blue **Deploy** button -> **New deployment**.
    *   Click the gear icon (⚙️) next to "Select type" and choose **Web app**.
    *   Configure it as follows:
        *   Execute as: **Me**
        *   Who has access: **Anyone**
    *   Click **Deploy**.
6.  **Authorize and Copy URL:** Authorize the script when prompted (you may need to click "Advanced" and "Go to... (unsafe)"). After deploying, **copy the Web app URL**.

### Step 3: Build Your Form

Go back to the Shellcraft Forms Creator and:
1.  Paste your **Google Script Web App URL** into the "General Settings".
2.  Customize the title, description, colors, and background.
3.  Add, edit, and reorder your questions. Make sure each question has a unique **ID** (this will become the column header in your Google Sheet).

### Step 4: Generate & Host Your Form

1.  Click the **Generate & Download HTML** button. This will save a single `shellcraft-form.html` file to your computer.
2.  Host this file anywhere you like! Some great free options include:
    *   [GitHub Pages](https://pages.github.com/)
    *   [Netlify Drop](https://app.netlify.com/drop)
    *   [Neocities](https://neocities.org/)

That's it! Your form is now live and will collect responses directly in your private spreadsheet.

## Technology Stack

This project is built with a focus on simplicity and portability, using:

*   **HTML5**
*   **CSS3** (with CSS Variables for easy theming)
*   **JavaScript (ES6+)**: No frameworks or heavy libraries.
*   **[SortableJS](https://github.com/SortableJS/Sortable):** For drag-and-drop functionality.

## 🤝 Contributing

Contributions are welcome! If you have an idea for a new feature, find a bug, or want to improve the code, please feel free to:

1.  Open an issue to discuss the change.
2.  Fork the repository and create a new branch.
3.  Submit a pull request with your improvements.

## License

This project is licensed under the **MIT License**. See the [LICENSE](LICENSE) file for details. You are free to use, modify, and distribute this software, but please keep the "Powered by Shellcraft Forms" attribution in the generated form to help support the project.
