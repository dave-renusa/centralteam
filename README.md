# RenUSA Harvest Hours Dashboard

Pulls hours from Harvest once a week, publishes a branded dashboard to GitHub
Pages, and flags anyone who is under or over your hour thresholds. Runs itself.
You just read it on Fridays.

The pull runs server-side inside GitHub Actions, so there is nothing to install
on your machine and no CORS proxy needed. Your Harvest token lives in GitHub as
an encrypted secret and is never written into any file.

## One-time setup (about 5 minutes)

1. **Create the repo.** In your `ddonofrio03` account, make a new repo (for
   example `harvest-dashboard`) and upload everything in this folder, keeping
   the structure intact (`.github/workflows/harvest.yml` must stay where it is).

2. **Get your two Harvest values.**
   - Account ID: visible at https://id.getharvest.com after you sign in.
   - Personal Access Token: create one at https://id.getharvest.com/developers.
     Generate the token while signed in as a user whose Harvest role can see
     Jeremy, Chad, and John's time. That means Administrator, or a Manager with
     access to their projects/people. A regular member token will only return
     your own hours.

3. **Add them as repo secrets** (this is where you enter the token, not me).
   In the repo: Settings > Secrets and variables > Actions > New repository
   secret. Add two secrets with these exact names:
   - `HARVEST_ACCOUNT_ID`
   - `HARVEST_TOKEN`

4. **Turn on Pages.** Settings > Pages > Build and deployment > Source: "Deploy
   from a branch" > Branch: `main`, folder: `/docs` > Save. Your dashboard will
   live at `https://ddonofrio03.github.io/harvest-dashboard/`.

5. **Run it once now.** Actions tab > "Harvest hours dashboard" > Run workflow.
   This overwrites the sample `docs/index.html` with your real numbers and
   confirms the token works before the first scheduled Friday run.

## Adjusting it

Open `harvest_dashboard.py` and edit the config block near the top:
- `TEAM_EMAILS` - leave empty to show every active user, or add Jeremy, Chad,
  and John's Harvest emails to show only them.
- `LOW_HOURS_WEEK` / `HIGH_HOURS_WEEK` - the thresholds that trigger a flag.

Change the schedule by editing the `cron` line in
`.github/workflows/harvest.yml`.

## Getting it emailed to you (optional)

The Action publishes the page; it does not send mail by itself, to avoid making
you manage SMTP secrets. Easiest path: set a weekly Cowork briefing that opens
the Pages URL each Friday and emails you the summary. Say the word and I will
write that briefing prompt for you.

## Notes and limits

- Output (what they actually shipped) is not in Harvest. The weekly update docs
  cover that. This dashboard is the hours half.
- Harvest API rate limit is generous for a weekly pull; the script paginates and
  will not hit it.
- I have not run this against your live account, so the first manual run is the
  real test. If the token lacks permission you will see a clear error in the
  Actions log rather than a silent empty dashboard.
