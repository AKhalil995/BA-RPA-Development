# BA-RPA-Development

A focused workspace showcasing:

- Hands-on UiPath automations (Dispatcher/Performer model, queue-driven bots, regex parsing, Modern UI selectors).
- Supporting assets that reflect Business Analysis thinking (config-driven design, audit trails, run logs).
- Verified training achievements (technical badges and certificates).

## Contents

- [RPA Projects](#rpa-projects)
  - [Grand Egyptian Museum Broadcast Email Bot](#grand-egyptian-museum-broadcast-email-bot-personalized-html-campaign-uipath)
  - [ACME Project (Dispatcher → Performer)](#acme-project-dispatcher--performer)
  - [RPA Challenge — Dynamic Form Filler](#rpa-challenge--dynamic-form-filler)
  - [ACME System — Work Items Automation & Regex Table Extraction](#acme-system--work-items-automation--regex-table-extraction-uipath)
  - [Weather Bot](#weather-bot-uipath)
  - [Select Assistant (Movies / Temperature)](#select-assistant-modular-workflow-uipath)
  - [ACME Work Items Scraper](#acme-work-items-scraper-uipath)
  - [Rotten Tomatoes — Top _N_ Movies](#rotten-tomatoes--top-_n_-movies-uipath)
- [Certificates & Badges](#-certificates--badges)

---

## RPA Projects

### Grand Egyptian Museum Broadcast Email Bot (Personalized HTML Campaign, UiPath)

Automated “national announcement” broadcast for the Grand Egyptian Museum (GEM) opening.

This bot sends a personalized, branded HTML email to each recipient, using Gmail SMTP securely (app password, not raw credentials), and produces an auditable delivery log.

**What this bot does**

- Loads config, narrative text, event metadata, SMTP settings, and the HTML email template from `Config.xlsx`.
- Reads all recipients (name + email) from `Recipients.xlsx`.
- For each recipient:
  - Replaces tokens like `{{FirstName}}`, `{{EventDate}}`, `{{VenueName}}`, `{{HeroImageUrl}}`, etc. in both the HTML body and subject line.
  - Injects ceremonial launch messaging (“Tomorrow, Egypt opens the doors…”), cultural context (Tutankhamun’s complete treasure collection, Ramesses II, Grand Staircase highlights), and practical notes (traffic restrictions, national-holiday notice).
  - Sends a mobile-friendly, museum-branded HTML email via Gmail SMTP.
  - Logs success/failure for that specific recipient.
- Builds a runtime log table (`dtLog`) so you can export a delivery report per run.

**Why this project matters**

- **Config-driven:** Non-developers can update messaging, images, venue details, etc. directly in Excel without touching workflows.
- **Personalized at scale:** Tokens in subject + body make the message feel like a direct invitation, not a mass blast.
- **Modern Gmail security:** Uses an app password from Orchestrator credential assets (compatible with Gmail 2FA).
- **Per-recipient isolation:** One bad address doesn’t kill the whole run — it’s just one `"Failed"` row in `dtLog`.

---

### ACME Project (Dispatcher → Performer)

<video
  src="RPA%20Projects/ACME%20Project%20%28Dispatcher%20-%20Performer%29/Demo.mp4"
  controls
  muted
  playsinline
  width="100%"
  poster="RPA%20Projects/ACME%20Project%20%28Dispatcher%20-%20Performer%29/Screenshots/Dispatcher%20Post%20Run%20-%20Queue%20Items.png">
Your browser doesn’t support HTML5 video.
<a href="RPA%20Projects/ACME%20Project%20%28Dispatcher%20-%20Performer%29/Demo.mp4">Download the MP4</a>.
</video>

**Folders**

- [`ACME_Dispatcher`](RPA%20Projects/ACME%20Project%20%28Dispatcher%20-%20Performer%29/ACME_Dispatcher/)
- [`ACME_Performer`](RPA%20Projects/ACME%20Project%20%28Dispatcher%20-%20Performer%29/ACME_Performer/)

**Screenshots**

<p float="left">
  <img src="RPA%20Projects/ACME%20Project%20%28Dispatcher%20-%20Performer%29/Screenshots/Dispatcher%20Post%20Run%20-%20Queue%20Items.png" alt="Dispatcher Post Run - Queue Items" width="32%" />
  <img src="RPA%20Projects/ACME%20Project%20%28Dispatcher%20-%20Performer%29/Screenshots/Guard%20Against%20Duplicates.png" alt="Guard Against Duplicates" width="32%" />
  <img src="RPA%20Projects/ACME%20Project%20%28Dispatcher%20-%20Performer%29/Screenshots/Performer%20Post%20Run%20-%20Status%20Update.png" alt="Performer Post Run - Status Update" width="32%" />
</p>

**What this does**

- **Dispatcher**
  - Logs into ACME, pulls Work Items.
  - Filters `Type = WI3`.
  - Builds a queue payload (WIID, Ref, key fields).
  - Pushes each transaction into Orchestrator Queue `ACME_WI3_Queue` (folder `ITI`).
- **Performer**
  - Consumes each queue item.
  - Encodes comment text to Base64 (via base64encode.org).
  - Updates the item in ACME UI: adds the encoded comment, sets **Status = Completed**.
  - Flags the queue transaction as Success/Business Rule Exception/System Exception.
  - Appends a structured CSV summary row.

**CSV output**

- Path: `Data\Output\Reports\ACME_Performer_{yyyyMMdd_HHmm}.csv`
- Columns: `WIID,Type,Status,Date,Result`
- The CSV is later attached/emailed for reporting and audit.

**Orchestrator triggers**

- **Queue Trigger:**  
  Process `ACME_Performer` runs automatically when `ACME_WI3_Queue` gets new items (Folder: `ITI`).
- **Email/Event Trigger:**  
  Can launch `ACME_Dispatcher` based on incoming email rules (e.g. specific From / Subject).

---

### RPA Challenge — Dynamic Form Filler

Solves the classic RPA Challenge (10 shuffled rounds of inputs). Uses anchor-by-label targeting so it still hits the correct field even when the UI layout changes each round.

**Modules**

- `DownloadAndMoveExcel.xaml`  
  Downloads the challenge Excel and exposes `out_ExcelPath` using `in_ChallengeURL`.
- `ReadExcelData.xaml`  
  Reads the Excel into a `DataTable` (`out_DataTable`).
- `ProcessFormData.xaml`  
  Opens the challenge page, loops each row, types the right value into the correct input using label anchors, and completes all 10 rounds.
- `Main.xaml`  
  Orchestrates the run and logging.

**Folder:** [`RPA Projects/RPA_Challenge`](RPA%20Projects/RPA_Challenge/)

---

### ACME System — Work Items Automation & Regex Table Extraction (UiPath)

Demonstrates UI navigation + code-based parsing.

**What it does**

- `OpenAcme.xaml` → Launches ACME and authenticates.
- `GoToWorkItems.xaml` → Navigates to Work Items, applies filters/pagination.
- `ExtractTables_Code.xaml` → Scrapes the Work Items table HTML/text and parses it into a clean structure using `Invoke Code` (C# with regex/string manipulation).
- `SaveResultToNotepad.xaml` → Saves the processed records to disk for quick review.
- `ACME_Workflow.xaml` → Orchestrator that wires the modules together and centralizes arguments/logging.

**Folder:** [`RPA Projects/ACME_Workflow_v2`](RPA%20Projects/ACME_Workflow_v2/)

---

### Weather Bot (UiPath)

Prompts the user for a City/Country → scrapes Google Weather → extracts **Temperature**, **Humidity**, **Status** → writes a formatted note into Notepad and saves it as  
`City - YYYY-MM-DD HH-mm.txt`.

- **Folder:** [`RPA Projects/Weather Bot`](RPA%20Projects/Weather%20Bot/)
- **Run:** Open `project.json` in UiPath Studio → restore dependencies → Run (Modern activities compliant).

---

### Select Assistant (Modular Workflow, UiPath)

Menu-driven bot that lets you choose functionality at runtime: **Movies Assistant** or **Temperature Assistant**.

**What it does**

- Shows an option selector (Movies / Temperature).
- **Movies Assistant**
  - Prompts for a movie title.
  - Scrapes IMDb for: Title, Year, Runtime, Rating, Popularity, URL, Plot, Directors, Writers, Stars.
  - Persists a timestamped `.txt` summary.
- **Temperature Assistant**
  - Prompts for City/Country.
  - Scrapes Google Weather (Temperature, Humidity, Status).
  - Saves a timestamped `.txt` summary.
- Shared module `SaveResultToNotepad.xaml` writes all outputs under `/Output`.

**Folder:** [`RPA Projects/Modular_Workflow-SelectAssistant`](RPA%20Projects/Modular_Workflow-SelectAssistant/)

---

### ACME Work Items Scraper (UiPath)

Logs into the ACME Test site, loops through **all Work Items pages**, and exports a CSV-style text file under `/Output`.

**Flow**

- `OpenAcme.xaml` → Launch & login.
- `GoToWorkItems.xaml` → Navigate to the Work Items listing (selectors hardened with anchors).
- `ExtractAllPages.xaml` → Crawl each page and regex-parse each row into:
  `WIID, Description, Type, Status, Date`.
- `SaveResultToNotepad.xaml` → Write output to a timestamped file.

**Folder:** [`RPA Projects/ACME`](RPA%20Projects/Modular_Workflow-SelectAssistant/ACME/)

---

### Rotten Tomatoes — Top _N_ Movies (UiPath)

Scrapes Rotten Tomatoes’ “300 Best Movies of All Time” editorial list and writes the **top N** results to disk as CSV text with columns: `Number, Name, Year`.

**What it does**

- Asks how many movies to fetch (10 / 50 / 100 / up to 300).
- Opens the page in Chrome and grabs the formatted text.
- Normalizes weird spacing (NBSP etc.).
- Uses regex to extract Rank, Title, Year.
- Saves the result through `SaveResultToNotepad.xaml` to  
  `/Output/RT_Top_{N}_{timestamp}.txt`.

**Folder:** [`RPA Projects/Rotten Tomatoes`](RPA%20Projects/Modular_Workflow-SelectAssistant/Rotten%20Tomatoes/)

---

## Certificates & Badges

### Badges

- TryHackMe — **Webbed**
- TryHackMe — **Cat Linux.text**
- TryHackMe — **Networking Nerd**
- Credly badge → https://www.credly.com/badges/64842f54-1c93-46b4-9bad-911ed7f252dd
- Credly badge → https://www.credly.com/badges/f879ba36-f9c3-48c4-bc68-63d42a26f83b

### Badge gallery

<p>
  <img src="Certificates%20&%20Badges/TryHackMe%20-%20Webbed.png" alt="TryHackMe - Webbed" height="64" />
  <img src="Certificates%20&%20Badges/TryHackMe%20-%20Cat%20Linux.text.png" alt="TryHackMe - Cat Linux.text" height="64" />
  <img src="Certificates%20&%20Badges/TryHackMe%20-%20Networking%20Nerd.png" alt="TryHackMe - Networking Nerd" height="64" />
</p>

> Each badge above corresponds to skills-based challenge tracks on TryHackMe (security/networking/Linux/web).  
> Credly badges are externally verifiable on the linked Credly pages.

### Certificates (PDFs)

| Title                                | Issuer                 |       Issued | Link                                                                                      | Verify                                                           |
| ------------------------------------ | ---------------------- | -----------: | ----------------------------------------------------------------------------------------- | ---------------------------------------------------------------- |
| HTML & CSS                           | MaharaTech             |            — | [PDF](<Certificates & Badges/Ahmed Khalil - Html&CSS - MaharaTech Cert.pdf>)              | —                                                                |
| RPA Using UiPath – Explorer Training | MaharaTech             |            — | [PDF](<Certificates & Badges/RPA Using UiPath - Explorer Training - MaharaTech.pdf>)      | —                                                                |
| C# (Basic)                           | HackerRank             |            — | [PDF](<Certificates & Badges/C_sharp (Basic) Certificate - HackerRank.pdf>)               | [_Verify_](https://www.hackerrank.com/certificates/e3f03e3fa969) |
| Python (Basic)                       | HackerRank             |            — | [PDF](<Certificates & Badges/Python (Basic) Certificate - HackerRank.pdf>)                | [_Verify_](https://www.hackerrank.com/certificates/bf56e7d36a2f) |
| Introduction to Cybersecurity        | Cisco Learning Academy | Aug 23, 2022 | [PDF](<Certificates & Badges/Cisco Learning Academy - Introduction to Cybersecurity.pdf>) | QR code on certificate                                           |
| Networking Basics                    | Cisco Learning Academy | Dec 03, 2022 | [PDF](<Certificates & Badges/Cisco Learning Academy - Networking Basics.pdf>)             | QR code on certificate                                           |

---

### Contact / Notes

- All projects here are built in UiPath Studio using Modern activities unless noted.
- Queues, credentials, and configs are parameterized so they can run in Orchestrator safely.
- This repo is meant to act as both:
  - **Portfolio** (to demonstrate delivery skills), and
  - **Reference** (to reuse components like Dispatcher/Performer patterns, Excel-driven configs, logging, etc.).
