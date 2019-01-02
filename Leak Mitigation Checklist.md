# Leak Mitigation Checklist

If you just leaked sensitive information in public source code, read this document as part of your emergency procedure. :fire:

Examples of sensitive information:
* API tokens
* Passwords
* Database credentials
* Database entries
* Confidential logs
* Confidential source code

If someone else leaked confidential information related to you or your company on GitHub, you may ask GitHub to perform a [DMCA takedown](https://help.github.com/articles/dmca-takedown-policy/#f-submitting-notices).

# Table of contents

[1. General advice](#1-general-advice)

 * [1.1. :warning: First things first: rotate your credentials](#11-warning-first-things-first-rotate-your-credentials)

 * [1.2. (Optional) Delete evidence of the leak](#12-optional-delete-evidence-of-the-leak)

   * [1.2.1 Why it may be important](#121-why-it-may-be-important)

   * [1.2.2 Detailed procedure](#122-detailed-procedure)

 * [1.3 Last but not least: review your access logs](#13-last-but-not-least-review-your-access-logs)

[2. Advice specific to a key](#2-advice-specific-to-a-key)

[3. Any question?](#3-any-question)

# 1. General advice

## 1.1. :warning: First things first: rotate your credentials

> Warning: Once you have pushed a commit to GitHub, you should consider any data it contains to be compromised.
> If you committed a password, change it! If you committed a key, generate a new one."
> ([Advice from GitHub :octocat: ](https://help.github.com/articles/removing-sensitive-data-from-a-repository/))

If you have pushed an API key, your first step should be to immediately revoke the compromised token.

It will NOT be enough to delete sensitive information in source code and commit changes or even purge your repository's history.

Keep in mind that you have to update your application accordingly in order not to break it. If your credentials are used by other developers or deployed in your infrastructure, make sure they all get a new version of it.

**If you are a junior developer, we highly recommend that you talk to your lead developer or the security guy. It's OK to make mistakes. Recognizing a mistake is the best way to show how much you care.**

## 1.2. (Optional) Delete evidence of the leak

### 1.2.1 Why it may be important

While you should first rotate your compromised credentials in all cases, you may wish to remove sensitive information from your git history as well. Remember that git is a versioning tool, which means that your commits history is searchable.

This is especially important if you committed sensitive data that cannot be changed (like any user data) or if you want to preserve your company's brand reputation and not raise cybersecurity concerns. Keep in mind that the Web never forgets: GitHub offers a [public events API](https://developer.github.com/v3/activity/events/#list-public-events) that can be used by everyone to download public commits. But still, we strongly advise limiting exposure of your sensitive information.

### 1.2.2 Detailed procedure

The procedure of rewriting git history can be a bit tricky, but this is a very good opportunity to learn about a few subtleties of the git protocol. Under its appearance of simplicity, the git protocol generally takes years for developers to be profoundly understood! So don't worry if you're a bit uncomfortable with it, and let's practice a little! :smile:

<details>
  <summary><b>Click to expand</b></summary>
<br>

**1. Either delete your repository or make it private**

It is often a good idea to buy yourself some time first: navigate to your GitHub repository then click ![settings](icons/github0.png) and in the "Danger Zone" at the bottom up click ![make private](icons/github00.png) to hide the repository from the public. If you don't have a GitHub paid account you may wish to make a backup then click on ![delete repository](icons/github000.png). You will push it back later.

**2. Rewrite git history**

We are going to use the well-known [BFG Repo-Cleaner](https://rtyley.github.io/bfg-repo-cleaner/).

> The BFG is a simpler, faster (10 - 720x faster) alternative to git-filter-branch for cleansing bad data out of your Git repository

Let's suppose you committed a sensitive file called **config.py**:

  i. Make sure you have java installed.

  ii. Clone your repository `git clone https://github.com/YOUR-USER-NAME/YOUR-REPOSITORY.git`.

  iii. The very latest commit on your current branch is protected by BFG so you have to make sure it is clean. Delete "config.py" and commit your changes `git commit -m "clean commit"`. Branches different than the current one are not protected so if "config.py" can be found on other branches, it will be cleaned by BFG.

  iv. Download [BFG](https://rtyley.github.io/bfg-repo-cleaner/).

  v. `java -jar bfg-VERSION.jar YOUR-REPOSITORY/.git --delete-files "config.py"`

  vi. Check your history. You can use the `git log -p` command to show the difference (called a "patch") introduced in each commit. If you navigate through your different branches, you should see that everything is fine.

  vii. You're not completely done yet. There is one safety net called `reflog` (for reference logs). After rewriting history, the reflog still contains information allowing you to go back to old states of branches. That's why you need to clean it and perform some garbage collection: `git reflog expire --expire=now --all && git gc --prune=now --aggressive`.

**3. Push your repository back**

Create a new repository and push it back. Make sure everybody deleted old clones and is using your new version.
</details>

## 1.3 Last but not least: review your access logs

It is a good idea to review the log data to see if there was some suspicious activity.

Some secrets can lead to other secrets. For example, Slack tokens can give access to messages and shared files generally containing other secrets. GitHub tokens can give access to private repositories also containing secrets.

Depending on your findings, if part of your infrastructure or data has been further exposed, you may need to take additional mitigation actions.

# 2. Advice specific to a key

> APIs are evolving quickly. While this project is actively maintained and contributors make their best efforts to keep it up to date, some information might be outdated or misleading. Create a pull request so we can make necessary corrections.

If you see any incorrect information, you are welcomed to [contribute](CONTRIBUTING.md).

**Table of contents**

[Alibaba](#-alibaba)

[Algolia](#-algolia)

[AWS](#-aws)

[DigitalOcean](#-digitalocean)

[Google](#-google)

[GitHub](#-github)

[GitLab](#-gitlab)

[Heroku](#-heroku)

[HubSpot](#-hubspot)

[Mailgun](#-mailgun)

[SendGrid](#-sendgrid)

[Slack](#-slack)

[Twilio](#-twilio)

[Twitter](#-twitter)

## <img src="icons/alibaba-logo.jpg" height="30" width="30" > Alibaba

### Revoke a key

Keys can be revoked on the [Access Key Management page](https://ak-console.aliyun.com/?spm=a2c63.p38356.a3.1.3cc6eb15icrFwA#/accesskey) in your console.

## <img src="icons/algolia-logo.jpg" height="30" width="30" > Algolia

### Revoke a key

The way to revoke a token depends on its type. There are two types of tokens:
* Regular API keys. These keys can be created from the dashboard.
* "Secured" API keys. These keys are used to allow users to see a filtered view of an index (useful when the index contains data from many users). They cannot be created from the dashboard. Instead, they must be generated at the API level (from your back-end). Such keys must be generated from any regular key (except the admin key) with a search scope (called the "parent key").

#### Your token is regular

You can revoke an API key from your [API keys dashboard](https://www.algolia.com/api-keys).

#### Your token is "secured"

Secured API keys can only be revoked by revoking their "parent" API key following the above procedure for regular tokens.

Revoking a parent key will revoke all its child keys.

## <img src="icons/aws-logo.jpg" height="30" width="30" > AWS

### Revoke a key

The way to revoke a token depends on the way it was issued.

#### The token was issued at the account level (root tokens)

Anyone who has such an access key has unrestricted access to all the account resources, including billing information.

Sign in to the [AWS Management Console](https://console.aws.amazon.com/) as the AWS account root user then choose your account name in the navigation bar, and then choose "My Security Credentials".

Expand the "Access keys" section then click on the ![delete](icons/aws2.png) button.

The difference between the ![delete](icons/aws2.png) button and the ![make inactive](icons/aws3.png) button is that disabled keys can be re-enabled later, which should not be the case here.

#### The token was issued for an IAM user

Sign in to the [AWS IAM Console](https://console.aws.amazon.com/iam/).

In the navigation pane, choose Users.

If necessary, add the Access key ID column to the users table:

  i. Click on the ![settings](icons/aws4.png) icon.

  ii. Select Access key ID.

  iii. Close.

Click on the desired user and choose the Security Credentials tab.

Click on the ![delete](icons/aws5.png) button then confirm.

### Check for suspicious activity

[AWS CloudTrail](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/using-cloudtrail.html) is the service logging API calls. When enabled, the service delivers the log files to an S3 bucket.

## <img src="icons/digitalocean-logo.jpg" height="30" width="30" > DigitalOcean

DigitalOcean personal access tokens don't allow granular access permissions which is one of the pillars of API security. When a token is issued, you can choose between only two scopes: read or read and write. Whatever the token scope, a lot of damage can be done and your token must be kept private. Tokens with write scope allow to spin up or delete servers, change [DNS records](https://www.digitalocean.com/community/tutorials/an-introduction-to-digitalocean-dns), ...

### Revoke a key
Go to your [API dashboard](https://cloud.digitalocean.com/settings/api/tokens) and next to your token click on **More** then on **Delete**.

### Check for suspicious activity

As far as we know, there is no way to check the last calls made with your API token. You can check your [security history dashboard](https://cloud.digitalocean.com/settings/security) or use [the actions API endpoint](https://developers.digitalocean.com/documentation/v2/#list-all-actions) to check a few actions performed on your account.


## <img src="icons/google-logo.jpg" height="30" width="30" > Google

As stated [here](https://support.google.com/cloud/answer/6310037?hl=en) you should not store API keys in the application's source tree. There are some exceptions like Google Maps API keys that sometimes need to be embedded in JS.

API keys are distributed per project. There are two types of personal secrets: API keys and Service account keys, the deletion process is the same and detailed below.

### Revoke a key

Go to the [credentials](https://console.cloud.google.com/apis/credentials) section of your project. Then select the corresponding API key and click on the delete button.

### Check for suspicious activity

Go to your [dashboard](https://console.cloud.google.com/apis/dashboard) and look at the traffic of the different APIs.

## <img src="icons/github-logo.png" height="30" width="30" > GitHub

### Revoke a key

Visit your [access tokens panel](https://github.com/settings/tokens) under your developer settings and click on the ![delete](icons/github.png) button.

### Check for suspicious activity

As far as we know, there is no way to check the last calls made with your API token. But GitHub offers the possibility to [review some security logs](https://help.github.com/articles/reviewing-your-security-log/). This is better than nothing, but this won't tell you if someone was able to access your private repositories for example.

## <img src="icons/gitlab-logo.jpg" height="30" width="30" > GitLab

### Revoke a key

Visit your [access tokens panel](https://gitlab.com/profile/personal_access_tokens) under your user settings and click on the ![revoke](icons/gitlab.png) button.

### Check for suspicious activity

If you self-host your GitLab you can access to system log files. API calls are logged in "api_json.log".

You can find the file in `/var/log/gitlab/gitlab-rails` for Omnibus GitLab packages or in `/home/git/gitlab/log` for installations from source. [More information](https://docs.gitlab.com/ee/administration/logs.html#api_json-log).

## <img src="icons/heroku-logo.png" height="30" width="30" > Heroku

Heroku API keys don't have scopes. They give full programmatic access to your account.

> Warning: we are not speaking about [Heroku OAuth](https://devcenter.heroku.com/articles/oauth) tokens which have [scopes](https://devcenter.heroku.com/articles/oauth#scopes). OAuth tokens are generated in the back-end, and unlike API keys, they are used with a client id.

### Revoke a key

To revoke your token go to your [account page](https://dashboard.heroku.com/account) under the "API Key" section, then click on ![regenerate API Key](icons/heroku-button.png). A new token will be issued.

## <img src="icons/hubspot-logo.jpg" height="30" width="30" > HubSpot

HubSpot keys give access to your account (an account is associated with a company, there is generally one account per company). There is one HubSpot key per account, even if the account has multiple users, which is not very convenient from a security point of view. When you revoke the token, make sure to keep your teammates, your infrastructure and/or your applications in sync.

### Revoke a key

To revoke your token go to your "Integrations" tab:

![integrations](icons/hubspot.png)

Then go to the "HubSpot API key" tab then click ![show key](icons/hubspot2.png) and ![deactivate](icons/hubspot3.png).

## <img src="icons/mailgun-logo.png" height="30" width="30" > Mailgun

Mailgun tokens give access to your mailing lists and your logs of sent emails. They allow sending emails with your configured [domain names](https://app.mailgun.com/app/domains).

### Revoke a key

Go to the [security panel](https://app.mailgun.com/app/account/security) in your console and click on the "Refresh token" button ![refresh](icons/mailgun.png) then on ![reset](icons/mailgun2.png).

### Check for suspicious activity

As far as we know, there is no way to check if an API key was used or not. The only thing you can do is check if your key was used to send emails in your Mailgun [logs panel](https://app.mailgun.com/app/logs).

## <img src="icons/sendgrid-logo.jpg" height="30" width="30" > SendGrid

### Revoke a key

Visit your [API keys page](https://app.sendgrid.com/settings/api_keys) then next to your key click on ![settings](icons/sendgrid.png) and on ![delete](icons/sendgrid2.png).

### Check for suspicious activity

As far as we know, there is no way to check if an API key was used or not. The only thing you can do is check if your key was used to send emails in your Sendgrid [email activity feed](https://sendgrid.com/docs/User_Guide/email_activity_feed.html).

## <img src="icons/slack-logo.jpg" height="30" width="30" > Slack

### Revoke a key

Slack tokens are very convenient because they have the power to revoke themselves thanks to the [auth.revoke API method](https://api.slack.com/methods/auth.revoke)! Easy as a command line:

```
curl "https://slack.com/api/auth.revoke?token=xoxp-YOUR-TOKEN-HERE"
```

### Check for suspicious activity

There is an [audit logs API](https://api.slack.com/docs/audit-logs-api) which is only available to [Enterprise Grid](https://api.slack.com/enterprise-grid) customers.

## <img src="icons/twilio-logo.jpg" height="30" width="30" > Twilio

### Revoke a key

The way to revoke a token depends on the way it was issued.

Twilio issues two different types of tokens:
* "Auth tokens" that are root tokens you can find under your [project settings](https://www.twilio.com/console/project/settings)
* "API keys" you can find in the [runtime panel](https://www.twilio.com/console/runtime/api-keys).

**Every time you go in the console, make sure to be on the right project. You can change your current project in the upper left corner.**

#### The token is an "Auth token"

Go to your [settings page](https://www.twilio.com/console/project/settings).

Naming conventions used by Twilio can be a bit misleading because to invalidate a token you first have to request a secondary token:

![request secondary token](icons/twilio.png)

Then click on "Request Token" in the pop-up:

![pop-up](icons/twilio2.png)

And then promote your secondary token to primary. As a side effect, your primary token will be revoked.

![promote to primary](icons/twilio3.png)

#### The token is not an "Auth token"

Go to your [runtime panel](https://www.twilio.com/console/runtime/api-keys).

Click on the SID associated with the token you want to revoke. Then click on ![delete](icons/twilio4.png).

### Check for suspicious activity

There is an [event log](https://www.twilio.com/console/runtime/audit-events) which is an Enterprise Plan feature.

## <img src="icons/twitter-logo.jpg" height="30" width="30" > Twitter

### Revoke a key

Go to your [application settings](https://twitter.com/settings/applications) and click on ![revoke access](icons/twitter.png).

# 3. Any question?

If something is not clear to you, it may not be clear for someone else either. Open an issue and let's discuss together! :smile:
