UAE Job Fit Analyzer
A one-textbox job-description analyzer with:
AI-generated role-fit report using the Gemini API
automatic logging of every JD and report to Google Sheets
a static front end that can be published with GitHub Pages
a Google Apps Script backend that keeps the API key out of the public repository
Architecture
`GitHub Pages (index.html)` → hidden form POST → `Google Apps Script Web App` → `Gemini API` → Google Sheet + report returned to the page.
Important: GitHub Pages is static hosting. The AI key and Google Sheet access must stay on the server side. Never put the Gemini API key in `index.html` or commit it to GitHub.
Setup overview
You need a Google account, a Google Sheet, a Gemini API key, and a GitHub account. The site will not work until you complete the setup and configure the Apps Script URL in `index.html`.
1. Create the Google Sheet
Create a new Google Sheet called `UAE Job Fit Analyzer`.
Copy the spreadsheet ID from its URL. It is the part between `/d/` and `/edit`.
2. Create the Gemini API key
Open Google AI Studio: https://aistudio.google.com/app/apikey
Create an API key and review the current pricing and usage limits for your account.
Keep the key private. Do not put it in GitHub or the front-end code.
The app uses the Gemini API from Apps Script. API availability, model names, limits, and pricing can change; check the current Google AI documentation if the model in `Code.gs` needs updating.
3. Create the Apps Script backend
Open https://script.google.com and create a new standalone project.
Replace the contents of `Code.gs` with the supplied `Code.gs` file.
Open Project Settings (gear icon) → Script properties → Add script property.
Add these properties:
`SPREADSHEET_ID` = your Google Sheet ID
`GEMINI_API_KEY` = your Gemini API key
Save the project.
In the function selector, choose `testSheetSetup` and click Run. Approve the requested Google Sheet permissions. The `JD Assessments` tab should be created.
Click Deploy → New deployment → select type Web app.
Set Execute as to Me. Set access to Anyone if you want your public GitHub Pages site to submit jobs without requiring each visitor to sign in. Only do this for your own personal app, and be aware that anyone who discovers the endpoint could potentially use your AI quota or write to your sheet. Do not share the endpoint widely. For stronger protection, add authentication or a secret/token and rate limiting before public use.
Click Deploy, authorize if prompted, and copy the Web App URL ending in `/exec`.
4. Connect the GitHub Pages front end
Open `index.html` in a text editor.
Find:
`const BACKEND_URL = "PASTE_YOUR_GOOGLE_APPS_SCRIPT_WEB_APP_URL_HERE";`
Replace the placeholder with your deployed Apps Script URL, keeping the quotes.
Save the file.
5. Publish with GitHub Pages
Create a new GitHub repository, for example `uae-job-fit-analyzer`.
Upload `index.html` to the repository root. You can also include `Code.gs` and this README for version control. The API key must never be included.
Open the repository Settings → Pages.
Under Build and deployment, choose Deploy from a branch.
Select branch `main` and folder `/ (root)`, then save.
Wait for GitHub Pages to publish. Your site URL will look like:
`https://YOUR-GITHUB-USERNAME.github.io/uae-job-fit-analyzer/`
What gets stored
Each successful analysis adds one row to the `JD Assessments` sheet with:
timestamp
request ID
first non-empty line of the JD
detected verdict (when it can be parsed)
detected fit score (when it can be parsed)
full pasted job description
full generated report
The website also has Copy report and Download .txt buttons.
Troubleshooting
“Setup needed”: the Apps Script URL has not been added to `index.html`.
Missing SPREADSHEET_ID / GEMINI_API_KEY: check Project Settings → Script properties.
API error or quota: check the Gemini API key, current model availability, billing/limits, and Apps Script execution logs.
The page waits but no result appears: verify the Web App deployment URL ends in `/exec`, access is configured as intended, and the deployed version includes the latest code.
Sheet stays empty: check Apps Script → Executions for errors and confirm the script was authorized to access the spreadsheet.
Public endpoint abuse: the simple version is designed for personal use. Before publishing to a broad audience, add authentication, a server-side shared secret, and rate limiting. A GitHub Pages URL is public by design.
Privacy and cost
A pasted JD and its report are sent to Google's Gemini API and stored in your Google Sheet. Review Google's current API terms and pricing. Use only job descriptions you are comfortable storing. A ChatGPT subscription does not automatically include a Gemini API key or API usage.
