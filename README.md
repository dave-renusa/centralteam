# RenUSA Central Team Hours Dashboard

Pulls hours from Harvest once a week, publishes a branded dashboard to GitHub
Pages, and flags anyone who is under or over your hour thresholds. Runs itself.
You just read it on Fridays.

This is a sibling of the `harvest` dashboard, pointed at a different group:
Mati, Josh, Kaylee, and Grace.

The pull runs server-side inside GitHub Actions, so there is nothing to install
on your machine and no CORS proxy needed. Your Harvest token lives in GitHub as
an encrypted secret and is never written into any file.

## One-time setup (about 5 minutes)

1. **Add the two Harvest secrets** (this is where you enter the token, not me).
   In this repo: Settings > Secrets and variables > Actions > New repository
   secret. Add two secrets with these exact names:
   - `HARVEST_ACCOUNT_ID` — visible at https://id.getharvest.com after sign-in.
   - `HARVEST_TOKEN` — create at https://id.getharvest.com/developers, generated
     while signed in as a user whose Harvest role can see Mati, Josh, Kaylee, and
     Grace's time. That means Administrator, or a Manager with access to their
     projects/people. A regular member token will only return your own hours.
     (The same admin token used by the `harvest` repo works here.)

2. **Turn on Pages.** Settings > Pages > Build and deployment > Source: "Deploy
   from a branch" > Branch: `main`, folder: `/ (root)` > Save. Your dashboard
   will live at `https://dave-renusa.github.io/centralteam/docs/index.html`.

3. **Run it once now.** Actions tab > "Harvest hours dashboard" > Run workflow.
   This generates the real `docs/index.html` and confirms the token works before
   the first scheduled Friday run.

## Adjusting it

Open `harvest_dashboard.py` and edit the config block near the top:
- `TEAM_EMAILS` - leave empty to show every active user, or list the Harvest
  emails to show only those people.
- `LOW_HOURS_WEEK` / `HIGH_HOURS_WEEK` - the thresholds that trigger a flag.

Change the schedule by editing the `cron` line in
`.github/workflows/harvest.yml` (currently Fridays at 18:00 UTC).

## Notes and limits

- Output (what they actually shipped) is not in Harvest. This dashboard is the
  hours half.
- Harvest API rate limit is generous for a weekly pull; the script paginates and
  will not hit it.
- The first manual run is the real test. If the token lacks permission you will
  see a clear error in the Actions log rather than a silent empty dashboard.
