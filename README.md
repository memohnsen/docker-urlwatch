# urlwatch

[urlwatch][1] is a tool for monitoring webpages for updates. This repository uses GitHub Actions to run urlwatch automatically on a schedule.

## Setup

1. **Fork or clone this repository** to your GitHub account

2. **Configure your URLs to watch** by editing `data/urls.yaml`

3. **Set up Slack notifications** (optional):
   - Go to https://api.slack.com/apps
   - Create a new app or select an existing one
   - Go to "Incoming Webhooks" and activate it
   - Add a new webhook to your workspace
   - Copy the webhook URL
   - Update `data/urlwatch.yaml` with your webhook URL:
     ```yaml
     report:
       slack:
         webhook_url: https://hooks.slack.com/services/YOUR/WEBHOOK/URL
         enabled: true
     ```

4. **Push to GitHub** - The workflow will automatically run every 30 minutes

## How It Works

- GitHub Actions runs the workflow on a schedule (every 30 minutes)
- urlwatch checks all URLs in `data/urls.yaml`
- When changes are detected, notifications are sent via Slack (if configured)
- The cache is stored between runs using GitHub Actions cache

## Configuration Files

### `data/urls.yaml`

Add websites to monitor. Each entry should look like:

```yaml
---
name: My Website
url: https://example.com
user_visible_url: https://example.com
filter:
- strip:
```

For more complex filtering, see the [urlwatch documentation][1].

### `data/urlwatch.yaml`

Main configuration file. Configure:
- Job defaults (error handling, etc.)
- Report settings (Slack, email, etc.)

### `data/hooks.py`

Custom Python hooks for advanced filtering and custom reporters.

## Customizing the Schedule

Edit `.github/workflows/urlwatch.yml` and change the cron schedule:

```yaml
on:
  schedule:
    - cron: '*/30 * * * *'  # Every 30 minutes
    # - cron: '*/15 * * * *'  # Every 15 minutes
    # - cron: '0 * * * *'     # Every hour
```

See [cron syntax documentation](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows#schedule) for details.

## Manual Runs

You can manually trigger a run from the GitHub Actions tab:
1. Go to your repository on GitHub
2. Click "Actions"
3. Select "URL Watch" workflow
4. Click "Run workflow"

## Testing Locally

To test your configuration locally:

```bash
# Install urlwatch
pip install urlwatch beautifulsoup4 html2text pyyaml requests keyring keyrings.alt lxml cssselect

# Create config directory
mkdir -p ~/.urlwatch

# Copy config files
cp data/urls.yaml ~/.urlwatch/urls.yaml
cp data/urlwatch.yaml ~/.urlwatch/urlwatch.yaml
cp data/hooks.py ~/.urlwatch/hooks.py

# Test
urlwatch --test-reporter slack
urlwatch --list
urlwatch  # Run a check
```

## Benefits Over Docker

- ✅ Runs 24/7 on GitHub's servers (no need to keep your computer on)
- ✅ Free for public repositories
- ✅ No local setup required
- ✅ Automatic updates when you push changes
- ✅ View run history and logs in GitHub

[1]: https://github.com/thp/urlwatch
