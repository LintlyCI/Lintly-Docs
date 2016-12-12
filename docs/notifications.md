# Notifications

Lintly can be configured to send notifications when a pull request reduces code quality by a certain percentage.

## Slack Notifications

To setup Slack notifications you will first need to create a new Integration in Slack. To do that,
go to your Slack installation at `https://{yourteam}.slack.com`. Next...

1. Click Apps & Integrations.
2. In the top right corner of the page, click Manage > Make a Custom Integration > Incoming WebHooks.
3. Choose the channel you would like notifications posted to and click "Add Incoming WebHooks Integration".
4. On the next page, copy the Webhook URL. This is what you will add to Lintly.

Now go back over to lintly.com. From here, follow these steps:

1. Go to the project you would like to configure Slack notifications for.
2. Click Settings
3. Under Notifications, click Slack
4. Enter your webhook URL, check the Enabled checkbox, and change the channel to something appropriate (e.g., `#general` or `#code-quality`).
5. Click Save Settings

## Email Notifications

To setup Email notifications, navigate to the project in Lintly and...

1. Click Settings
2. Under Notifications, click Email
3. Enter the email addresses you would like to receive emails (separated by a comma) and check the Enabled checkbox.
4. Click Save Settings