# cPanel Git Deployment Guide

This guide will help you deploy the Simon repository to your domain using cPanel's Git Version Control.

## Prerequisites

- Access to cPanel for your hosting account
- Your domain configured and pointing to your hosting
- A GitHub Personal Access Token (for authentication)

## Step 1: Create GitHub Personal Access Token

1. Go to GitHub: https://github.com/settings/tokens
2. Click **"Generate new token"** → **"Generate new token (classic)"**
3. Give it a name (e.g., "cPanel Deployment")
4. Select scope: **`repo`** (Full control of private repositories)
5. Click **"Generate token"**
6. **Copy the token immediately** (you won't see it again!)

## Step 2: Set Up Git Repository in cPanel

1. **Log into cPanel**
   - Navigate to your hosting control panel

2. **Access Git Version Control**
   - Find the "Git Version Control" icon under the "Files" section
   - Click to open

3. **Create Repository**
   - Click the **"Create"** button
   - Fill in the following details:

   ```
   Clone URL: https://github.com/nzdog/Simon.git
   Repository Path: public_html
   Repository Name: Simon
   ```

   > **Note:** If deploying to a subdirectory, use `public_html/subdirectory-name` instead

4. **Authenticate**
   - Username: `nzdog` (your GitHub username)
   - Password: Paste your Personal Access Token (not your GitHub password!)

5. **Click "Create"**

## Step 3: Configure Deployment Path

The `.cpanel.yml` file in this repository controls how files are deployed.

**Current Configuration:**
```yaml
deployment:
  tasks:
    - export DEPLOYPATH=/home/yourusername/public_html/
    - /bin/cp -R * $DEPLOYPATH
```

**IMPORTANT:** You need to update the `.cpanel.yml` file:

1. Replace `yourusername` with your actual cPanel username
2. Common paths:
   - Root domain: `/home/yourusername/public_html/`
   - Subdomain: `/home/yourusername/public_html/subdomain/`
   - Addon domain: `/home/yourusername/public_html/addondomain.com/`

To find your cPanel username:
- Look at the top-right corner of cPanel
- Or check the path in File Manager

## Step 4: Initial Deployment

1. In cPanel Git Version Control, find your **Simon** repository
2. Click **"Manage"**
3. Click **"Pull or Deploy"**
4. Click **"Deploy HEAD Commit"**
5. Wait for deployment to complete
6. Visit your domain to verify deployment

## Step 5: Set Up Automatic Deployments (Optional)

Configure webhooks so cPanel automatically deploys when you push to GitHub:

### In cPanel:
1. Go to your repository in Git Version Control
2. Click **"Manage"**
3. Copy the **Deployment Webhook URL** (it looks like: `https://yourdomain.com:2083/cpsess...`)

### In GitHub:
1. Go to: https://github.com/nzdog/Simon/settings/hooks
2. Click **"Add webhook"**
3. Paste the webhook URL from cPanel
4. Content type: `application/json`
5. SSL verification: Enable
6. Events: Select **"Just the push event"**
7. Active: Check the box
8. Click **"Add webhook"**

Now every time you push to GitHub, cPanel will automatically pull and deploy!

## Project Structure

Your website includes:
- `index.html` - Lichen Reflection feedback survey (homepage)
- `onboarding-questionnaire.html` - Field questionnaire
- `feedback-thank-you.html` - Feedback form thank you page
- `thank-you.html` - General thank you page

## Netlify Forms Configuration

Your forms use Netlify's form handling. If you're deploying to cPanel instead of Netlify, you'll need to:

**Option 1: Use a Form Handler Service**
- Formspree: https://formspree.io/
- Basin: https://usebasin.com/
- Getform: https://getform.io/

**Option 2: Set Up Server-Side Form Processing**
- Use PHP or another server-side language to process forms
- Store submissions in a database or email them

**To Update Forms for PHP Processing:**
1. Create a `process-form.php` file
2. Update form action in HTML files from `/feedback-thank-you.html` to `process-form.php`
3. Remove Netlify-specific attributes: `data-netlify="true"` and `netlify-honeypot="bot-field"`

## Testing Deployment

After deployment:

1. **Check Homepage:** Visit your domain (e.g., `https://yourdomain.com`)
2. **Test All Pages:**
   - Main feedback form: `https://yourdomain.com/`
   - Onboarding: `https://yourdomain.com/onboarding-questionnaire.html`
   - Thank you pages: Check form submissions redirect correctly

3. **Verify Form Functionality:**
   - Forms will need updating if not using Netlify (see above)
   - Test form submissions once configured

## Updating Your Site

### Method 1: Push to GitHub (with webhook)
```bash
# Make changes locally
git add .
git commit -m "Your commit message"
git push origin master
# cPanel automatically deploys!
```

### Method 2: Manual Deployment
1. Push changes to GitHub
2. Go to cPanel Git Version Control
3. Click "Manage" on your repository
4. Click "Pull or Deploy"
5. Click "Update from Remote"
6. Click "Deploy HEAD Commit"

## Troubleshooting

### Permission Issues
If files aren't copying:
```bash
chmod -R 755 /home/yourusername/public_html/
```

### Wrong Path
If site doesn't appear:
- Double-check your username in `.cpanel.yml`
- Verify the deployment path in cPanel File Manager

### Forms Not Working
- Remember: Netlify forms only work on Netlify
- You must implement alternative form processing (see above)

### Webhook Not Triggering
- Check webhook status in GitHub repository settings
- Verify the webhook URL is correct
- Check cPanel logs for errors

## Support

If you encounter issues:
1. Check cPanel Error Logs (Metrics → Errors)
2. Review deployment logs in Git Version Control interface
3. Contact your hosting provider's support if needed

## Security Notes

- Never commit your Personal Access Token to the repository
- Keep `.cpanel.yml` file secure (contains deployment paths)
- Regularly update your GitHub token if compromised
- Use strong passwords for cPanel access
