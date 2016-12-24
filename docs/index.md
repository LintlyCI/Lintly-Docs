# Lintly

Welcome to the Lintly documentation!

## Supported Languages

Lintly currently supports **Python 2 only**. There are plans to add support for more languages in the future,
but Python 3 support will need to come first.

## Authentication and Permissions

Lintly uses GitHub OAuth tokens to authenticate users. These tokens will also be used to pull data
from GitHub when the user is browsing around on Lintly. Therefore, if a user does not have access to
your repo in GitHub then they will not have access in Lintly.

This extends to Admin users as well. Admins of a repo in GitHub will be admins in Lintly. Admins can
perform the following actions in Lintly:

* Import repos from GitHub into Lintly
* Remove repos from Lintly
* Update settings

## Configuration

Lintly uses the Python tool [flake8](http://flake8.readthedocs.io/en/latest/) under the hood. Therefore,
flake8 configuration files will work with Lintly as well. Changing the configuration will affect which
violations count against your code quality score. To change the configuration, add either a `.flake8`,
`setup.cfg`, or `tox.ini` file to the root directory of your repo. [Read more about flake8 configuration here](http://flake8.readthedocs.io/en/latest/user/configuration.html).

### Default configuration

If your project does not have a flake8 configuration file then Lintly will add one for you. The following
is the default flake8 config file that Lintly provides:

```
[flake8]
# Be a little more relaxed than the PEP8 79 character limit
max-line-length = 100

# McCabe complexity max
max-complexity = 10

# Only check Python files
filename = *.py

# Lintly requires the default format
format = default

# Don't count against Django's generated migration files
exclude =
    */migrations/*
```

## Notifications

Lintly can be configured to send notifications when a pull request reduces code quality by a certain percentage.

### Slack Notifications

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

### Email Notifications

To setup Email notifications, navigate to the project in Lintly and...

1. Click Settings
2. Under Notifications, click Email
3. Enter the email addresses you would like to receive emails (separated by a comma) and check the Enabled checkbox.
4. Click Save Settings

## Frequently Asked Questions

### How do I add new users to Lintly?

Lintly uses GitHub APIs to authorize users, so there is no need to explicitly add a user in Lintly.
If the user has read access to the repo in GitHub then they will have access in Lintly.

### How is the code quality score calculated?

Code Quality = Lines of Code with Issues / Total Lines Of Code * 100.0
