# Nextcloud FEFS App Store

This repository automatically maintains a merged Nextcloud app store JSON that includes all official Nextcloud apps plus the FEFS Cockpit app.

## Public App Store URL

Use this URL as a custom app store in your Nextcloud instance:

```
https://raw.githubusercontent.com/Fonds-Europe/nextcloud-fefs-appstore/main/apps.json
```

## How It Works

### Automatic Updates

The app store is automatically updated through GitHub Actions:

1. **Daily Updates**: Every day at 2 AM UTC, the workflow fetches the latest official Nextcloud app store
2. **Release Triggers**: When a new FEFS Cockpit release is published, it automatically triggers an update
3. **Manual Trigger**: Can be manually triggered via GitHub Actions

### Workflow Architecture

```
┌─────────────────────────────────┐
│  FEFS Cockpit Repository        │
│  New Release Published           │
└────────────┬────────────────────┘
             │
             │ repository_dispatch
             │
             ▼
┌─────────────────────────────────┐
│  FEFS App Store Repository      │
│  Workflow Triggered              │
└────────────┬────────────────────┘
             │
             ├─► Fetch Official Apps
             │   (apps.nextcloud.com)
             │
             ├─► Fetch Latest FEFS Release
             │   (GitHub API)
             │
             ├─► Merge JSON Files
             │
             └─► Commit & Push apps.json
```

## Configuration in Nextcloud

To use this custom app store in your Nextcloud instance:

1. Edit your Nextcloud config.php
2. Add the custom app store URL:

```php
'appstoreurl' => 'https://raw.githubusercontent.com/Fonds-Europe/nextcloud-fefs-appstore/main/apps.json',
```

Or run via occ command:

```bash
php occ config:system:set appstoreurl --value="https://raw.githubusercontent.com/Fonds-Europe/nextcloud-fefs-appstore/main/apps.json"
```

## FEFS Cockpit Repository Setup

To enable automatic triggers from the FEFS Cockpit repository:

### 1. Create Personal Access Token

1. Go to GitHub Settings → Developer settings → Personal access tokens → Tokens (classic)
2. Generate new token with `repo` scope
3. Save the token securely

### 2. Add Secret to FEFS Cockpit Repository

1. Go to FEFS Cockpit repository → Settings → Secrets and variables → Actions
2. Create new repository secret:
   - Name: `GHP_TOKEN`
   - Value: Your personal access token

### 3. Add Workflow to FEFS Cockpit

Add this workflow file to `.github/workflows/trigger-appstore-update.yml` in the FEFS Cockpit repository:

\`\`\`yaml
name: Trigger App Store Update

on:
  release:
    types: [published]

jobs:
  trigger-appstore:
    runs-on: ubuntu-latest

    steps:
      - name: Trigger app store update
        run: |
          curl -X POST \
            -H "Accept: application/vnd.github+json" \
            -H "Authorization: Bearer \${{ secrets.GHP_TOKEN }}" \
            -H "X-GitHub-Api-Version: 2022-11-28" \
            https://api.github.com/repos/Fonds-Europe/nextcloud-fefs-appstore/dispatches \
            -d '{"event_type":"new-release","client_payload":{"version":"\${{ github.event.release.tag_name }}","tarball_url":"\${{ github.event.release.tarball_url }}"}}'

      - name: Log trigger
        run: |
          echo "✓ Triggered app store update for version \${{ github.event.release.tag_name }}"
\`\`\`

## Manual Update

You can manually trigger an update:

1. Go to Actions tab in this repository
2. Select "Update App Store" workflow
3. Click "Run workflow"

## App Store Contents

- **Official Nextcloud Apps**: ~8,700+ apps from apps.nextcloud.com
- **FEFS Cockpit**: European Funds Cockpit application

## File Structure

```
.
├── .github/
│   └── workflows/
│       └── update-appstore.yml    # Main workflow
├── apps.json                       # Merged app store (auto-generated)
└── README.md                       # This file
```

## Monitoring

Check the Actions tab to see:
- Update history
- Success/failure status
- Number of apps in each update

## License

This repository is maintained by Fonds Europe for the FEFS Cockpit project.

## Support

- FEFS Cockpit Issues: https://github.com/Fonds-Europe/nextcloud-fefs-cockpit/issues
- App Store Issues: https://github.com/Fonds-Europe/nextcloud-fefs-appstore/issues
