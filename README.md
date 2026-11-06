# UAM

A web-based Active Directory management console, rebuilt as a standalone, front-end-only demo for my portfolio.

The original is an internal enterprise tool I designed and built so service desk and L3 engineers could look up accounts, unlock users and reset passwords from a browser, without needing a full admin workstation or VDI session. This repository contains a **sanitised mockup**: the layout and workflows are the same, but all data is fictional and nothing talks to a real directory.

> **Live demo:** add your GitHub Pages link here after you publish (see [Run it](#run-it)).

<!-- Add a screenshot: ![Search results](docs/search.png) ![User properties](docs/user.png) -->

## What it does

| Area | Details |
|---|---|
| **Directory search** | Search by name, logon, email, employee number, title, department or office. Results table with a Properties link per account. |
| **Account properties** | Identity, contact, organisation, password and lockout details in collapsible sections (Properties, Extended properties, Memberships / Direct reports, Credentials, History). |
| **Unlock account** | One-click unlock. Status badges update immediately and the action is written to the activity log. |
| **Password reset** | Modal with required-field and match validation, show/hide toggle, and a secure password generator (length 10–20, upper/lower/numbers/symbols) using `crypto.getRandomValues`. |
| **Manager lookup** | Manager details modal with a shortcut to open the manager's own properties. |
| **Mail objects** | Proxy addresses (primary flagged), managed objects, public delegates and delegates BL. |
| **Safety prompts** | Detects terminated or disabled accounts from the description and shows the termination checklist. Detects ticket numbers (`INC…`, `RITM…`) and warns the agent they may hold history. |
| **Audit trail** | Every property view, unlock and reset is added to the account's log table. |
| **Excel export** | Extract any table to `.xlsx` with a timestamped filename (SheetJS). |
| **Theming** | Light and dark mode follow the system setting. |

## Demo data

All people, emails, servers and devices are made up (`contoso-demo.com`, `corp.demo`). Good searches to try:

| Search | Shows |
|---|---|
| `reyes` | A normal account with memberships, devices, proxy addresses and delegates |
| `wong` | A **locked-out** account to try the unlock flow |
| `nair` | A **terminated** account that triggers the termination warning |
| `carter` | An account with a **ticket number** in the description |
| `jardiolin` | The portfolio owner, including a certifications section |

State is held in memory, so refreshing the page resets everything.

## Run it

It is a single static file with no build step.

**Locally:** open `index.html` in any modern browser.

**GitHub Pages:**
1. Rename `directory-console.html` to `index.html` (if you haven't already).
2. Push to a GitHub repository.
3. In the repository go to **Settings → Pages**, choose your branch and the `/ (root)` folder, and save.
4. Your demo will be at `https://<your-username>.github.io/<repo-name>/`.

The Excel export loads SheetJS from the cdnjs CDN, so the Extract buttons need an internet connection.

## Tech stack

**This mockup:** HTML, CSS and vanilla JavaScript in one file. SheetJS (`xlsx` 0.18.5) is the only dependency, loaded from a CDN.

**Original production version:**
- C# / ASP.NET Web Forms (async page lifecycle) on IIS
- REST API layer exposing directory data as JSON
- PowerShell (`System.Management.Automation`) running `Unlock-ADAccount` and `Set-ADAccountPassword` against the correct domain controller
- jQuery, DataTables and Bootstrap on the client
- Per-action CSV audit logging with the signed-in agent's identity

## How the mockup maps to the real thing

| Original | Mockup |
|---|---|
| Search page calls a REST endpoint and renders the JSON as a table | Filters an in-memory array and renders the same columns |
| Details page loads one account by GUID and server | Loads one record by id from the URL hash |
| PowerShell runspace executes AD cmdlets using the agent's credentials | JavaScript functions change in-memory state |
| CSV log file written on the server | Log table updated in the page |
| Windows-authenticated agent identity | Fixed `demo.agent` label |

## Security notes for the real tool

The mockup keeps the interaction design but deliberately leaves out the sensitive parts. If you build something similar, consider:

- Authenticate and authorise every agent, and limit unlock and reset rights by role and scope.
- Never store reusable credentials in session state. Prefer delegated permissions or a service account with tightly scoped rights.
- HTML-encode every directory value before rendering it, since attributes like `description` are user-editable.
- Keep an immutable audit log of who performed each action, on which account, and when.
- Require identity verification before a reset, and never display a generated password to anyone except the user.

## Project structure

```
.
├── index.html   # the whole app (HTML + CSS + JS)
└── README.md
```

## Author

**Christian Joseph Jardiolin**
Systems Engineer & Cloud Solutions Architect, Metro Manila, Philippines

- Email: christianjardiolin@outlook.com
- LinkedIn: [christian-jardiolin](https://www.linkedin.com/in/christian-jardiolin/)

Open to part-time and contract remote roles aligned to Australian and APAC hours.

## Licence

Released under the MIT Licence. Add a `LICENSE` file if you want this to be official, or change this section to suit.
