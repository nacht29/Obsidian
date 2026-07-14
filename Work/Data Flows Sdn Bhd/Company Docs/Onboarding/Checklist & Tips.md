# Checklist & Tips

Onboarding checklist and practical tips for new joiners at Data Flows.

---

## Before Your First Day

- [x] Bring your own **laptop & charger** to the office on Day 1
- [x] Dress in **casual attire** (neat and presentable — think jeans + T-shirt + sneakers; suitable for a casual client meeting)
- [ ] Check with the HR team regarding your **hybrid working arrangement** before onboarding day
- [ ] Set up all required internal systems and accounts (see Checklist Part 2 below) **before** onboarding day

---

## First Day Agenda

| Time | Activity |
|------|---------|
| 9:00 am | HR introduction |
| 9:30 am | Office tour |
| 9:45 am | Brief introduction of company and job scope from each supervisor |
| 12:30 pm | Lunch break |
| 1:30 pm | Read and digest the onboarding slides; familiarise with tools and platforms |
| 6:00 pm | Done for the day |

---

## Checklist Part 1 — Must-Do

- [ ] Read the onboarding slides & staff handbook
- [ ] Read the **[[Work/Data Flows Sdn Bhd/Company Docs/Onboarding/Password Policy|Password Policy]]** (NEW PASSWORD PROTECTION POLICY, 26 Sept 2025) — mandatory before proceeding
- [ ] Set up **2FA** on all logins (e.g. Google Authenticator)
- [ ] Get added to the main DF WhatsApp group (group admin will add you)
- [ ] Get access to Kakitangan (HR system) and view org chart
- [ ] Login to ClickUp
- [ ] Get GitLab access credentials from team lead and login
- [ ] Review git branching and deployment practices with team
- [ ] Subscribe to the DF Google Calendar (see Email & Calendar Setup below)
- [ ] **Submit bank account details for payroll** — email to veon@dataflows.co with: Bank Name, Account Holder Name, Account Number

---

## Checklist Part 2 — System & Account Setup

### Email Signature (Mandatory)

Data Flows uses a standardised email signature for all internal and external emails. Set it up before your first day.

**Step 1:** Access the **Email Signature Generator** tool (link provided by HR)

**Step 2:** Fill in all your details accordingly

**Step 3:** Click the **Submit** button under STEP TWO

**Step 4:** An email signature block is generated under STEP THREE — click **Select All**, then copy with `Ctrl+C` (Windows) or `Cmd+C` (macOS)

**Step 5:** Go to your Gmail account → click the **Settings gear icon** (top-right corner)

**Step 6:** Click **See all settings**

**Step 7:** In the Settings page, under the **General** tab, scroll down to find **Signature**

**Step 8:** Click **+ Create new** → name it `DF Signature`

**Step 9:** Paste the signature you copied in Step 4 into the text editor

**Step 10:** Scroll to the bottom of the page → click **Save Changes**

---

### DF Calendar

Subscribe to the DF Google Calendar to see team leaves and events. Visit the link provided in the onboarding slides (or from HR).

You may also create and add DF-related individual leaves and team events/meetings to this calendar in the future. It is shared with everyone.

---

### SentinelOne (Mandatory Security Agent)

SentinelOne is the company's Endpoint Detection and Response (EDR) tool — installation is **non-negotiable** and must be done on your working machine.

**Download installers:**
- macOS: https://drive.google.com/file/d/1FnKEXStlPpAc7I_ut5WIHrBCHQKMg35x/view?usp=drive_link
- Windows: https://drive.google.com/file/d/1CSFHSynk13nhB31oA5ictQMgzKPy6jDw/view?usp=drive_link

**Site Token** (needed during installation):
```
eyJ1cmwiOiAiaHR0cHM6Ly9hcHNlMi0xMDA0LnNlbnRpbmVsb25lLm5ldCIsICJzaXRlX2tleSI6ICI1MmQzMDBlYWVlN2Q4NDk5ZTAzMGI1YWE2ZGY3ZDU5MzlhMjVmYTUxMWRhMjliMWMwOWM3OWQ0OTJmY2Y5MzRlIn0=
```

**Windows installation (Windows 10/11):**
1. Locate the downloaded installer file (`SentinelAgent.exe` or `SentinelInstaller.msi`)
2. Open **Command Prompt** or **PowerShell** as **Administrator**
3. Run: `SentinelAgent.exe /SITE_TOKEN="[PASTE_SITE_TOKEN_HERE]" /Q`
4. Installation runs silently in the background (~5–10 minutes); restart usually not required

**macOS installation (Ventura 13+ / Sonoma 14+):**
1. Double-click the downloaded `SentinelAgent.pkg` and follow on-screen prompts; enter the Site Token when asked
2. **Mandatory final step — Grant Full Disk Access:**
   - Go to System Settings → Privacy & Security → Full Disk Access
   - Click the lock to make changes
   - Ensure these SentinelOne components are added with the toggle **ON**:
     - `sentineld`
     - `sentinel_shell`
     - `sentinel_helper`

---

### Gemini Pro (AI Tool)

Data Flows subscribes to Gemini Pro.
- Access: gemini.google.com/app → login with company email
- **Important:** Do NOT pass sensitive info (patient details, credentials, passwords, API keys) to Gemini Pro or any AI tools

---

### Postman API

Used for API testing.
- Use your **personal Postman account** for daily use
- Company account has a collection of APIs across projects
  - Request credentials from team leader via email
  - After login, go to "My Workspace" for all API collections
  - Session limit: ~3 active sessions at once on desktop version
  - **Workaround:** Open company account in browser incognito mode; use personal account on desktop app

---

### Aria — HR Assistant

Aria is Data Flows' AI assistant for HR queries. Get instant automated replies for HR-related questions.

- **WhatsApp:** +1 (231) 707-8988
- **Passcode:** team123

---

### HR Resources (Google Drive)

All important HR documents and updates are on the shared Google Drive folder **"INT | DF Handbook & Policies"**:

https://drive.google.com/drive/folders/1DBfC_EkNk121a7fOwveD1FRWvcNZT8b7?usp=sharing

Contents include: HR Handbook & Policies, Onboarding Materials, Company Procedures, Announcements.

---

### Kakitangan (HRMS)

Kakitangan is the HR management system used for:
- **Leave:** Apply for leave, view balance, track approvals
- **Payroll:** View and download payslips, YTD amounts, EA forms
- **Claims:** Submit expense receipts, track claim status

---

## Checklist Part 3 — Projects

- [ ] Get added to internal project WhatsApp group
- [ ] Get access to project Google Drive and key spreadsheets
- [ ] Browse project files to understand scope and flow
- [ ] Get user access for staging/local (admin, doctors, pharmacist roles, etc.)
- [ ] Install relevant IDE, frameworks, and libraries (check version with team — e.g., **Microsoft Visual Studio** + .NET for MYHA)
- [ ] Setup project locally and verify login works

> Note: Large image files may load slowly. Use Irfanview or similar viewer.

---

## Monitor Setup (Large External Monitor)

1. Switch on the monitor power plug
2. Press the "on" button behind the monitor
3. Plug in the HDMI cable to your laptop (HDMI label facing up)
4. Right-click desktop → Display Settings
5. Scroll to "Multiple displays" → select **Extend these displays** (not Duplicate) → Apply
6. Move windows to the external monitor as needed

> The monitor can output laptop audio. Volume control button is on the monitor (joystick-style, above the power button).

---

## How to Handle Tickets

1. Read the ticket requirements thoroughly
2. Understand the project domain and technical terms
3. Clarify requirements and ask questions if unsure
4. Study team standards and look at similar resolved tickets
5. Find where changes need to be made — check the app flow in staging or local first
6. Identify the expected outcome(s)
7. Explore the relevant codebase and identify change points
8. Create pseudocode
9. Break work into smaller parts
10. Consult seniors/tech lead to validate approach if unsure
11. Document your development process in the ticket for reference
12. Clone a **fresh branch from production** before starting work
13. Start with tests if TDD is used
14. Make small, frequent commits with meaningful messages
15. Git pull / fetch the latest production branch before pushing
16. After pushing: add repo link in ClickUp and tag team lead / seniors for code review

> **Ticket prioritization:** If a client is actively testing a reopened/add-on ticket, prioritize that over fresh tickets (assess severity case-by-case).

---

## Advisable Practices

- Make an Excel spreadsheet to track daily tasks, work done, completion time, and notes — useful for performance reviews
- Download the ClickUp mobile app to stay updated
- Document your steps and findings; good docs help the whole team
- Monthly Friday sharing sessions — randomly chosen employees present a topic

---

## Programming Tips

- Avoid yak shaving (going down rabbit holes of unnecessary side tasks)
- Keep code simple and readable
- Progress > perfection
- Structure commits for easier code review
- Ask the team when stuck — common errors are often already known
