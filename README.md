# BA-RPA-Development

A personal workspace for RPA learning and proofs-of-concept + a curated list of my verified certificates/badges.

- [RPA Projects](#rpa-projects)
- [Certificates & 🏅 Badges](#certificates--badges)

---

## RPA Projects

### Weather Bot (UiPath)

Prompts for a city/country → searches Google Weather → extracts **Temperature**, **Humidity**, **Status** → writes a note in **Notepad** and saves it as  
`City - YYYY-MM-DD HH-mm.txt`.

- **Folder:** [`RPA Projects/Weather Bot`](RPA%20Projects/Weather%20Bot/)
- **Run:** open `project.json` in UiPath Studio → restore packages → run **Modern** or **Classic** implementation.

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

**Run:** open `project.json` in UiPath Studio → _Restore Dependencies_ → run **Main Flow.xaml** entry point.

---

### ACME Work Items Scraper (UiPath)

Logs into the **ACME Test** site, navigates to **Work Items**, extracts all pages, and saves a CSV-like text (one line per item) to the `/Output` folder.

**What it does**

- `OpenAcme.xaml` → launches Chrome and signs in (Modern UI).
- `GoToWorkItems.xaml` → navigates to the Work Items list (selectors/anchors hardened).
- `ExtractAllPages.xaml` → auto-detects or honors a page limit, scrapes table text, parses rows with regex into:  
  `WIID, Description, Type, Status, Date`.
- `SaveResultToNotepad.xaml` → writes results to a timestamped file in `/Output`.

**Files (entry & modules)**  
`ACME_Workflow.xaml` (entry), `OpenAcme.xaml`, `GoToWorkItems.xaml`, `ExtractAllPages.xaml`, `Modules/SaveResultToNotepad.xaml`

**Folder:** [`RPA Projects/ACME`](RPA%20Projects/ACME/) _(adjust if your repo uses a different path)_

**Run**

1. Open the ACME project in UiPath Studio and **Set Entry Point** to `ACME_Workflow.xaml`.
2. In **Arguments**, set:
   - `in_strUsername` (string)
   - `in_strPassword` (string)
   - `in_intTotalPages` (int, optional: `0` or empty = auto-detect)
3. Run. Output saves under `/Output/` (timestamped filename).

**Tech highlights**

- Modern UI (`NApplicationCard`, anchors, resilient selectors)
- Optional page cap vs. auto-detected pagination
- Regex parsing with named groups → clean CSV text

---

### Rotten Tomatoes — Top _N_ Movies (UiPath)

Scrapes the **Rotten Tomatoes “300 Best Movies of All Time”** page and saves the **top N** entries as CSV text with columns: `Number, Name, Year`.

**What it does**

- Prompts with an **Input Dialog** for how many movies to fetch (e.g., 10, 50, 100; configurable up to 300).
- Opens the editorial page in Chrome (Modern UI), **gets page text with formatting**, normalizes NBSP spacing.
- Uses a regex to parse each line into **rank, title, year**.
- Builds a CSV in memory and saves it through `SaveResultToNotepad.xaml` to `/Output/RT_Top_{N}_{timestamp}.txt`.

**Files**  
`GetTopMoviesFromRT.xaml`, `Modules/SaveResultToNotepad.xaml`

**Folder:** [`RPA Projects/Rotten Tomatoes`](RPA%20Projects/Rotten%20Tomatoes/)

**Run**

1. Open the RT project (or just `GetTopMoviesFromRT.xaml`) in UiPath Studio.
2. Click **Run**, enter a number when prompted (1–300).
3. Check `/Output/` for the timestamped text file.

**Tech highlights**

- Modern UI scraping with `AllowFormatting`
- NBSP cleanup + regex-based parsing
- Quirk-safe CSV (titles sanitized/quoted as needed)

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
