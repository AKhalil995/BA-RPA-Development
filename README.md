# BA-RPA-Development

A personal workspace for RPA learning and proofs-of-concept + a curated list of my verified certificates/badges.

- [RPA Projects](#rpa-projects)
- [Certificates & 🏅 Badges](#certificates--badges)

---

## RPA Projects

# ACME Project (Dispatcher → Performer)

->
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

---

**Folders**

- [`ACME_Dispatcher`](RPA%20Projects/ACME%20Project%20%28Dispatcher%20-%20Performer%29/ACME_Dispatcher/)
- [`ACME_Performer`](RPA%20Projects/ACME%20Project%20%28Dispatcher%20-%20Performer%29/ACME_Performer/)

---

### Screenshots

<p float="left">
  <img src="RPA%20Projects/ACME%20Project%20%28Dispatcher%20-%20Performer%29/Screenshots/Dispatcher%20Post%20Run%20-%20Queue%20Items.png" alt="Dispatcher Post Run - Queue Items" width="32%" />
  <img src="RPA%20Projects/ACME%20Project%20%28Dispatcher%20-%20Performer%29/Screenshots/Guard%20Against%20Duplicates.png" alt="Guard Against Duplicates" width="32%" />
  <img src="RPA%20Projects/ACME%20Project%20%28Dispatcher%20-%20Performer%29/Screenshots/Performer%20Post%20Run%20-%20Status%20Update.png" alt="Performer Post Run - Status Update" width="32%" />
</p>

### What this does

- **Dispatcher** pulls ACME Work Items, **filters Type = WI3**, builds queue payload (Ref + key fields), and enqueues to **`ACME_WI3_Queue`** (folder **`ITI`**).
- **Performer** gets each queue item, **encodes comment text (Base64 via base64encode.org)**, opens the work-item **Update** page, pastes into **Add Comment**, sets **Status = Completed**, **updates transaction status**, and **appends to a CSV** (run summary emailed on finish).

---

### CSV output

- **Path:** `Data\Output\Reports\ACME_Performer_{yyyyMMdd_HHmm}.csv`
- **Columns:** `WIID,Type,Status,Date,Result`
- Every successful transaction appends a line; the file is attached to the end-of-run email.

### Orchestrator triggers

- **Queue Trigger** → Process: `ACME_Performer`, Queue: `ACME_WI3_Queue`, Folder: `ITI`, Min items = 1.
- **Event Trigger (Gmail)** → to kick off the **Dispatcher** on specific incoming emails (filters by `From`/`Subject`).

---

### RPA Challenge — Dynamic Form Filler

Solves the classic **RPA Challenge** dynamic input form (10 shuffled rounds) and anchor‑by‑label targeting. The workflow reads the provided Excel, then types each value into the correct field even as positions change between rounds.

**What it does**

- `DownloadAndMoveExcel.xaml` → downloads the challenge Excel and exposes its path via `out_ExcelPath` (uses `in_ChallengeURL`).
- `ReadExcelData.xaml` → reads the Excel into a `DataTable` (`in_ExcelPath` → `out_DataTable`).
- `ProcessFormData.xaml` → opens the challenge page (`in_ChallengeURL`), loops the data, and **types into inputs anchored to their labels**; handles all **10 rounds** reliably.
- `Main.xaml` orchestrates the end‑to‑end run and structured logging.

---

**Folder:** [`RPA Projects/RPA_Challenge`](RPA%20Projects/RPA_Challenge/)

---

### ACME System — Work Items Automation & Regex Table Extraction (UiPath)

End‑to‑end **ACME System** workflow that logs in, navigates to **Work Items**, parses tables using **Invoke Code (C#)** with string/regex methods, and exports structured results. Designed to showcase activities with code‑only parsing where appropriate.

**What it does**

- `OpenAcme.xaml` → launches ACME URL and performs login if needed.
- `GoToWorkItems.xaml` → navigates to the Work Items listing and applies filters/pagination if required.
- `ExtractTables_Code.xaml` → scrapes the HTML/text and **parses rows in code** (C#) via regex/string methods; outputs a clean table (e.g., CSV/DataTable).
- `SaveResultToNotepad.xaml` → persists the processed results to a .txt file for quick verification.
- `ACME_Workflow.xaml` → orchestrator that calls the above modules, sets arguments, and handles logging.

---

**Folder:** [`RPA Projects/ACME_Workflow_v2`](RPA%20Projects/ACME_Workflow_v2/)

---

### Weather Bot (UiPath)

Prompts for a city/country → searches Google Weather → extracts **Temperature**, **Humidity**, **Status** → writes a note in **Notepad** and saves it as  
`City - YYYY-MM-DD HH-mm.txt`.

- **Folder:** [`RPA Projects/Weather Bot`](RPA%20Projects/Weather%20Bot/)
- **Run:** open `project.json` in UiPath Studio → restore packages → run **Modern** or **Classic** implementation.

---

---

### Select Assistant (Modular Workflow, UiPath)

A **menu-driven, modular RPA bot** that lets you choose between two assistants at runtime:
**Movies** or **Temperature**. It demonstrates clean separation into reusable workflows and a
shared save routine.

**What it does**

- Presents a selector (Movies / Temperature) and routes to the chosen assistant.
- **Movies Assistant** → prompts for a title → fetches details from IMDb (Title, Year, Runtime, Rating, Popularity, URL, Plot, Directors, Writers, Stars) → writes a formatted note.
- **Temperature Assistant** → asks for City/Country → scrapes Google Weather (Temperature, Humidity, Status) → writes a formatted note.
- Uses a shared module **`SaveResultToNotepad.xaml`** to persist results with timestamped filenames in `/Output`.

**Folder:** [`RPA Projects/Modular_Workflow-SelectAssistant`](RPA%20Projects/Modular_Workflow-SelectAssistant/)

---

### ACME Work Items Scraper (UiPath)

Logs into the **ACME Test** site, navigates to **Work Items**, extracts all pages, and saves a CSV-like text (one line per item) to the `/Output` folder.

**What it does**

- `OpenAcme.xaml` → launches Chrome and signs in.
- `GoToWorkItems.xaml` → navigates to the Work Items list (selectors/anchors hardened).
- `ExtractAllPages.xaml` → auto-detects or honors a page limit, scrapes table text, parses rows with regex into:  
  `WIID, Description, Type, Status, Date`.
- `SaveResultToNotepad.xaml` → writes results to a timestamped file in `/Output`.

**Folder:** [`RPA Projects/ACME`](RPA%20Projects/Modular_Workflow-SelectAssistant/ACME/)

---

### Rotten Tomatoes — Top _N_ Movies (UiPath)

Scrapes the **Rotten Tomatoes “300 Best Movies of All Time”** page and saves the **top N** entries as CSV text with columns: `Number, Name, Year`.

**What it does**

- Prompts with an **Input Dialog** for how many movies to fetch (e.g., 10, 50, 100; configurable up to 300).
- Opens the editorial page in Chrome, **gets page text with formatting**, normalizes NBSP spacing.
- Uses a regex to parse each line into **rank, title, year**.
- Builds a CSV in memory and saves it through `SaveResultToNotepad.xaml` to `/Output/RT_Top_{N}_{timestamp}.txt`.

**Folder:** [`RPA Projects/Rotten Tomatoes`](RPA%20Projects/Modular_Workflow-SelectAssistant/Rotten%20Tomatoes/)

---

## 🎓 Certificates & 🏅 Badges

### Badges (with verification)

- TryHackMe — **Terminaled** → https://tryhackme.com/plightwellplace/badges/terminaled
- TryHackMe — **Network Fundamentals** → https://tryhackme.com/plightwellplace/badges/network-fundamentals
- Credly badge → https://www.credly.com/badges/64842f54-1c93-46b4-9bad-911ed7f252dd
- Credly badge → https://www.credly.com/badges/f879ba36-f9c3-48c4-bc68-63d42a26f83b

### Badge gallery

_(click any image to open the official verification page)_

<p>
  <a href="https://tryhackme.com/plightwellplace/badges/terminaled">
    <img src="Certificates%20&%20Badges/232b582a4941f96d9470532131cc7bf5.png" alt="TryHackMe: Terminaled" height="64">
  </a>
  <a href="https://tryhackme.com/plightwellplace/badges/network-fundamentals">
    <img src="Certificates%20&%20Badges/410ab5d30c6974680febb9c51586a897.png" alt="TryHackMe: Network Fundamentals" height="64">
  </a>
  <a href="https://www.credly.com/badges/64842f54-1c93-46b4-9bad-911ed7f252dd">
    <img src="Certificates%20&%20Badges/b471211ad1dade21d7bd9bcde7684c84.png" alt="Credly badge" height="64">
  </a>
</p>

### Certificates (PDFs)

| Title                             | Issuer                       |           Issued | Link                                                                         | Verify                                                           |
| --------------------------------- | ---------------------------- | ---------------: | ---------------------------------------------------------------------------- | ---------------------------------------------------------------- |
| HTML & CSS                        | MaharaTech                   |                — | [PDF](<Certificates & Badges/Ahmed Khalil - Html&CSS - MaharaTech Cert.pdf>) | —                                                                |
| C# (Basic)                        | HackerRank                   |                — | [PDF](<Certificates & Badges/C_sharp (Basic) Certificate - HackerRank.pdf>)  | [_Verify_](https://www.hackerrank.com/certificates/e3f03e3fa969) |
| Python (Basic)                    | HackerRank                   |                — | [PDF](<Certificates & Badges/Python (Basic) Certificate - HackerRank.pdf>)   | [_Verify_](https://www.hackerrank.com/certificates/bf56e7d36a2f) |
| **Introduction to Cybersecurity** | **Cisco Networking Academy** | **Aug 23, 2022** | [PDF](<Certificates & Badges/I2CSUpdate20251001-31-j8t3l9.pdf>)              | QR code on certificate                                           |
| **Networking Basics**             | **Cisco Networking Academy** | **Dec 03, 2022** | [PDF](<Certificates & Badges/NetworkingBasicsUpdate20251001-30-teqsep.pdf>)  | QR code on certificate                                           |
