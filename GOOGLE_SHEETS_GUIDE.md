# Setting up Google Sheets Response Tracker

To store your survey responses in Google Sheets, follow these steps:

### 1. Create a Google Sheet
1. Create a new Google Sheet at [sheets.new](https://sheets.new).
2. Name it "Stafford Web Survey Responses".
3. The first row (headers) will be automatically created by the script, or you can pre-fill them:
   `Timestamp`, `Full Name`, `Email`, `Phone`, `Address`, `Affiliation`, `Connections`, `Connection Other`, `Q1`, `Q2`, `Q3`, `Concerns`, `Q5`, `Comments`, `Support Choice`, `Contact Consent`, `Public Name Consent`, `Keep Private`

### 2. Create the Apps Script
1. In your Google Sheet, go to **Extensions** > **Apps Script**.
2. Delete any existing code and paste the following:

```javascript
function doPost(e) {
  try {
    var sheet = SpreadsheetApp.getActiveSpreadsheet().getSheets()[0];
    var data;
    
    // Attempt to parse JSON first, then fall back to form parameters
    if (e.postData && e.postData.contents) {
      try {
        data = JSON.parse(e.postData.contents);
      } catch (f) {
        data = e.parameter;
      }
    } else {
      data = e.parameter;
    }
    
    // Define headers in the order you want them
    var headers = [
      "Timestamp", "Full Name", "Email", "Phone", "Address", "Affiliation", 
      "Connections", "Connection Other", "Q1", "Q2", "Q3", "Concerns", 
      "Q5", "Comments", "Support Choice", "Contact Consent", 
      "Public Name Consent", "Keep Private"
    ];
    
    // Set headers if the sheet is empty
    if (sheet.getLastRow() === 0) {
      sheet.appendRow(headers);
    }
    
    // Prepare the row data
    var row = [
      new Date(),
      data.full_name || "",
      data.email || "",
      data.phone || "",
      data.address || "",
      data.affiliation || "",
      data.connection || "",
      data.connection_other || "",
      data.q1 || "",
      data.q2 || "",
      data.q3 || "",
      data.concerns || "",
      data.q5 || "",
      data.comments || "",
      data.support_choice || "",
      data.contact_consent || "",
      data.public_name_consent || "",
      data.keep_private || ""
    ];
    
    sheet.appendRow(row);
    
    return ContentService.createTextOutput(JSON.stringify({ "result": "success" }))
      .setMimeType(ContentService.MimeType.JSON);
      
  } catch (error) {
    Logger.log(error.toString());
    return ContentService.createTextOutput(JSON.stringify({ "result": "error", "error": error.toString() }))
      .setMimeType(ContentService.MimeType.JSON);
  }
}
```

### 3. Deploy (IMPORTANT)
1. Click **Deploy** > **New deployment**.
2. Select **Type**: **Web app**.
3. **Description**: Stafford Survey Handler v2.
4. **Execute as**: **Me**.
5. **Who has access**: **Anyone**.
6. Click **Deploy**.
7. **Copy the new Web App URL**. (Note: Every time you change the code, you MUST create a NEW deployment or update the version to see changes).

### 4. Update the Website
1. Open `index.html`.
2. Find the `<script>` tag at the bottom (or where you added the form handler).
3. Replace `PASTE_YOUR_GOOGLE_SCRIPT_URL_HERE` with the URL you copied.
