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

### GitHub API tokens

Lintly uses GitHub API tokens to interact with GitHub. The following tokens are used in Lintly:

**The currently logged in user**

* All API requests that occur while logged into Lintly.com. This includes determining repository permissions,
importing projects, and viewing file contents.

**The project's [authorized user](#project-authorized-users)**

* All API requests that occur during automatic builds, except for PR comments/reviews.

**[lintly-bot](https://github.com/lintly-bot)**

* Commenting or creating a pull request review during automatic builds on public repositories.

### Project authorized users

The authorized user of a project is the user whose API tokens will be used during automatic builds.
This is initially set as the user who imported the project into Lintly.

**Note:**  Some organizations may have a bot account whose API tokens should be used during automatic
builds and when commenting on pull requests. Soon there will be a way to specify who your project's
authorized user is.

## Build Configuration

Individual builds can be configured using a file called `lintly.yml`. This file should be at the top
level of the project.

## Linter Configuration

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

## Pull Requests

When a pull request is opened on GitHub, Lintly will get notified via a web hook and begin linting the changes. If there are any violations found then Lintly will comment on the pull request. There are two ways that Lintly can comment on pull requests:

1. [Pull request reviews](https://help.github.com/articles/about-pull-request-reviews/) (comment on individual lines)
2. A single comment with all violations

Lintly's GitHub bot account ([lintly-bot](https://github.com/lintly-bot)) will first attempt to create a pull request review. With a pull request review, the pull request will receive a comment on each line that has a code quality violation. If **lintly-bot** does not have permission to create a pull request review on your repo then it will fall back to creating one pull request comment that contains all violations.

### Enabling Pull Request Reviews

Due to [a restriction in the GitHub API](https://help.github.com/articles/permission-levels-for-a-user-account-repository/), **lintly-bot** must be invited to be a collaborator on your repository before it can create a pull request review. Follow these steps to invite **lintly-bot** to be a collaborator:

1. Go to your repo in GitHub
2. Click Settings
3. Click Collaborators
4. Enter the name `lintly-bot` into the search bar
5. Click Add Collaborator

This will send an invitation to **lintly-bot**. These invitations must be accepted manually, so please allow a few hours for the invitation to be accepted.

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

## Project Badges

Project badges are a handy way to show the current code quality percentage of your app. This is what
they look like:

![Code Quality badge](https://img.shields.io/badge/lintly-100%-brightgreen.svg)

To create your badge, go to your project in Lintly. In the top-right corner is your project badge.
Clicking on that will take you to the project badges page. From here, copy the Markdown, HTML, or RST
and paste it in your README file.

## Frequently Asked Questions

### How do I add new users to Lintly?

Lintly uses GitHub APIs to authorize users, so there is no need to explicitly add a user in Lintly.
If the user has read access to the repo in GitHub then they will have access in Lintly.

### How is the code quality score calculated?

Code Quality = Lines of Code with Issues / Total Lines Of Code * 100.0
