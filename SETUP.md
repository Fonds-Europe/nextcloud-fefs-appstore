# GHP_TOKEN Setup Guide

## What is it?

The `GHP_TOKEN` is a GitHub Personal Access Token (GHP) that allows the FEFS Cockpit repository to trigger updates in the App Store repository when you publish a new release.

## Step-by-Step Setup

### Step 1: Create a Personal Access Token

1. Go to GitHub Settings:
   - Click your profile picture (top right)
   - Click **Settings**
   - Scroll down to **Developer settings** (bottom of left sidebar)
   - Click **Personal access tokens**
   - Click **Tokens (classic)**

2. Generate new token:
   - Click **Generate new token (classic)**
   - You may need to confirm your password

3. Configure the token:
   - **Note**: `FEFS Cockpit App Store Trigger` (or any name you want)
   - **Expiration**: Choose your preferred expiration (recommend: No expiration or 1 year)
   - **Select scopes**: Check **`repo`** (this gives full control of private repositories)
     - This will automatically check all sub-options under `repo`

4. Generate:
   - Scroll to bottom and click **Generate token**
   - **IMPORTANT**: Copy the token immediately! You won't be able to see it again
   - It looks like: `ghp_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`

### Step 2: Add Token to FEFS Cockpit Repository

1. Go to the FEFS Cockpit repository:
   ```
   https://github.com/Fonds-Europe/nextcloud-fefs-cockpit
   ```

2. Click **Settings** tab (top navigation)

3. In left sidebar, click **Secrets and variables** → **Actions**

4. Click **New repository secret** button

5. Enter secret details:
   - **Name**: `GHP_TOKEN` (must be exactly this)
   - **Secret**: Paste the token you copied earlier
   - Click **Add secret**

### Step 3: Verify Setup

Once the token is added:

1. You should see `GHP_TOKEN` listed in repository secrets
2. The token value will be hidden (shown as `***`)
3. The next time you publish a release, it will automatically trigger the app store update

## How It Works

```
You publish a new release in FEFS Cockpit
              ↓
GitHub Actions workflow runs
              ↓
Uses GHP_TOKEN to authenticate
              ↓
Sends notification to App Store repository
              ↓
App Store repository updates automatically
```

## Security Notes

- The token is stored securely as a GitHub Secret
- It's encrypted and only accessible to repository workflows
- It's never exposed in logs or outputs
- Only users with admin access to the repository can view/edit secrets

## Troubleshooting

### If the trigger doesn't work:

1. **Check token exists**: Go to Settings → Secrets and variables → Actions
2. **Verify name is correct**: Must be exactly `GHP_TOKEN`
3. **Check token permissions**: Should have `repo` scope
4. **Check token expiration**: Token may have expired

### To regenerate token:

1. Go back to your Personal Access Tokens page
2. Find your token and click **Regenerate token**
3. Copy the new token
4. Update the `APPSTORE_TOKEN` secret in the repository

## Alternative: Organization Access Token (Optional)

If you prefer to use an organization token instead of personal:

1. Go to Organization settings
2. Click **Developer settings** → **Personal access tokens**
3. Create a fine-grained token with repository access
4. Follow the same steps above to add it as a secret

## Need Help?

- GitHub PAT Documentation: https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token
- GitHub Secrets Documentation: https://docs.github.com/en/actions/security-guides/encrypted-secrets
