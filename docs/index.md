# Lintly

Welcome to the Lintly documentation!

If you need clarification on any documentation then please
[create an issue here](https://github.com/LintlyCI/Lintly-Issues/issues).

## Supported Languages

Lintly currently supports **Python 2 and 3** and **JavaScript**.

If you would like to request a language or linter then please create a [feature request here](https://github.com/LintlyCI/Lintly-Issues/issues).

## Authentication and Permissions

Lintly uses OAuth tokens to authenticate users. These tokens will also be used to pull data from
GitHub or GitLab when the user is browsing around on Lintly. Therefore, if a user does not have access to
your repo in GitHub or GitLab then they will not have access in Lintly.

This extends to Admin users as well. Admins of a repo will be admins in Lintly. Admins can perform
the following actions in Lintly:

* Import repos from GitHub or GitLab into Lintly
* Remove repos from Lintly
* Update settings

### GitHub/GitLab API tokens

Lintly uses API tokens to interact with the GitHub/GitLab API. The following tokens are used in Lintly:

**The currently logged in user**

* All API requests that occur while logged into Lintly.com. This includes determining repository permissions,
importing projects, and viewing file contents.

**The project's [authorized user](#project-authorized-users)**

* All API requests that occur during automatic builds (except for PR comments/reviews) **unless a custom
bot is specified**.

**[lintly-bot](https://github.com/lintly-bot)**

* Commenting or creating a pull request review during automatic builds on public repositories **unless
a custom bot is specified**.

**[Custom bot](#custom-bot-accounts)**

* All API requests

### Project authorized users

The authorized user of a project is the user whose API tokens will be used during automatic builds.
This is initially set as the user who imported the project into Lintly.

**Note:**  Some organizations may have a bot account whose API tokens should be used during automatic
builds and when commenting on pull requests. Soon there will be a way to specify who your project's
authorized user is.

## Build Configuration

Builds can be configured by adding a file named `lintly.yml` to root of your project.

### Default configuration

Lintly uses the following default configuration for builds:

```yaml
lintly:
  bot: null

languages:
  python:
    version: 2

notifications:
  commit_status:
    enabled: true
  pr_comments:
    enabled: true
  email:
    enabled: false
    score_decreases_percentage: 0.5
    addresses:
      - youremail@here.com
  slack:
    enabled: false
    score_decreases_percentage: 0.5
    webhook_url: ""
    channel: "#general"
```

### Enabling linters

You can enable and disable linters in the `lintly.yml` file using the following structure:

```yaml
linters:
  eslint:
    enabled: true
  flake8:
    enabled: true
```

If you specify linters in your config file then Lintly will only use those. Otherwise, Lintly will
use a [default set of linters](#default-linters).

#### Default linters

If you do not specify which linters you would like to use then Lintly will make an educated guess.
The default linters are determined based on the languages your code is written in. Lintly will lint
any language that makes up at least 25% of your codebase at the time you enable the project in Lintly.

For example, if your project is made up of 75% Python, 15% JavaScript, and 10% HTML then Lintly will
only lint Python. If your project is made up of 40% Python, 40% JavaScript, and 20% HTML then Lintly
will lint both Python and JavaScript.

### Python 3 configuration

Add the following configuration to your `lintly.yml` file to make Lintly lint your project using
Python 3.6.

```yaml
languages:
  python:
    version: 3
```

### Custom bot accounts

You can use the API tokens from your own bot accounts to retrieve PR diffs, comment on PRs, create PR
reviews, and create [commit statuses](https://developer.github.com/v3/repos/statuses/). This will save
the API tokens on your personal account from being used to much and potentially being rate-limited by
GitHub.

Before adding a custom bot, **ensure that the bot account has collaborator access to your repository**
in GitHub. This is a security feature to ensure the bot account you're using really does have the
appropriate access to the repo.

To add a custom bot to Lintly, first login into Lintly with the bot account. You need to do this so
that Lintly will have access to the bot's API token. Next, add the following YAML to your `lintly.yml`
file (substituting `{bot_username}` with the GitHub username of your bot):

```yaml
lintly:
  bot: {bot_username}
```

With custom bot accounts you will **now have commit statuses and pull request reviews in your pull
requests**. Commit statuses only work with custom bots, as the default lintly-bot account likely won't
have collaborator access to create them.

## Python configuration

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

## JavaScript configuration

Lintly uses [ESLint](http://eslint.org) to lint JavaScript. Regular [ESLint configuration](eslint.org/docs/user-guide/configuring)
will work with Lintly as well.

### Default configuration

If your project does not have an ESLint configuration file then Lintly will add one for you. The following
is the default ESLint config file that Lintly provides:

```json
{
    "rules": {},
    "env": {
        "es6": true,
        "browser": true
    },
    "extends": "eslint:recommended"
}
```

## Pull Requests

When a pull request is opened on GitHub or GitLab, Lintly will get notified via a web hook and begin linting the changes. If there are any violations found then Lintly will comment on the pull request. There are two ways that Lintly can comment on pull requests:

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

Now, add the webhook URL and channel to your `lintly.yml` file. **Note:** The `channel` attribute
needs to be in quotes since the `#` character denotes comments in YAML`.

```yaml
notifications:
  slack:
    enabled: true
    score_decreases_percentage: 0.5
    webhook_url: {webhook_url}
    channel: "#yourchannel"
```

### Email Notifications

To setup Email notifications, add the following configuration to your `lintly.yml` file:

```yaml
notifications:
  email:
    enabled: true
    score_decreases_percentage: 0.5
    addresses:
      - first_email@yourdomain.com
      - another_email@yourdomain.com
```

## Project Badges

Project badges are a handy way to show the current code quality percentage of your app. This is what
they look like:

![Code Quality badge](https://img.shields.io/badge/lintly-100%-brightgreen.svg)

To create your badge, go to your project in Lintly. In the top-right corner is your project badge.
Clicking on that will take you to the project badges page. From here, copy the Markdown, HTML, or RST
and paste it in your README file.

## Frequently Asked Questions

### How do I add new users to Lintly?

Lintly uses GitHub/GitLab APIs to authorize users, so there is no need to explicitly add a user in Lintly.
If the user has read access to the repo in GitHub/GitLab then they will have access in Lintly.

### How is the code quality score calculated?

Code Quality = Lines of Code with Issues / Total Lines Of Code * 100.0
