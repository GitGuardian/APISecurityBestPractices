# Good development practices

Good development practices to reduce the risks of leaking a secret.

## Table of contents

  * [Avoid git add * commands](#avoid-git-add--commands)
  * [Name sensitive files in .gitignore and .npmignore](#name-sensitive-files-in-gitignore-and-npmignore)
  * [Store your secrets in a safer place](#store-your-secrets-in-a-safer-place)
    + [Use local environment variables, when feasible](#use-local-environment-variables-when-feasible)
    + [Store your secrets encrypted in a git repository](#store-your-secrets-encrypted-in-a-git-repository)
    + [Use a "secrets as a service" solution](#use-a-secrets-as-a-service-solution)
  * [Use temporary credentials](#use-temporary-credentials)
  * [Whitelist your IPs](#whitelist-your-ips)
  * [Restrict permissions associated with your keys](#restrict-permissions-associated-with-your-keys)
  * [Protect your development / testing secrets as well as your production secrets](#protect-your-development--testing-secrets-as-well-as-your-production-secrets)
  * [Don't share secrets over emails, Slack, Skype, etc.](#dont-share-secrets-over-emails-slack-skype-etc)
  * [Use GitHub scanning tools](#use-github-scanning-tools)
  
## Avoid git add * commands

Using wildcards can easily capture files you do not want to share.

Instead of wildcards, name each file you want to add, for example: `git add README.MD`. Before committing your changes, use `git status` command to list track and untracked files. When you commit with `git commit`, your untracked files will not end up in source control.

## Name sensitive files in .gitignore and .npmignore

GitHub published a collection of useful [.gitignore templates](https://github.com/github/gitignore).

See these examples for [Python](https://github.com/github/gitignore/blob/master/Python.gitignore) or [Go](https://github.com/github/gitignore/blob/master/Go.gitignore).

Here are some filenames that are frequently associated with leaks:  [.travis.yml](https://docs.travis-ci.com/user/customizing-the-build#The-Build-Lifecycle) files, [Dockerfiles](https://docs.docker.com/engine/reference/builder/), [docker-compose.yml](https://docs.docker.com/get-started/part3/#your-first-docker-composeyml-file), .env files, config.py, [.zshrc](https://doc.ubuntu-fr.org/zsh), [Jenkinsfiles](https://jenkins.io/doc/book/pipeline/jenkinsfile/), ...

If you use npm to develop your project, see how to use [.npmignore](https://docs.npmjs.com/misc/developers#keeping-files-out-of-your-package).

## Store your secrets in a safer place

### Use local environment variables, when feasible

When possible, use environment variables to hold your sensitive tokens, keeping these out of source control.

If you use Heroku, see how to use environment variables: [Heroku configuration variables]( https://devcenter.heroku.com/articles/config-vars).

**Advantages**

It's simple.

**Disadvantages**

This approach may not be feasible at scale because there is no way to easily keep developers, applications and/or infrastructure in sync.

### Store your secrets encrypted in a git repository

**Advantages**

* Your secrets are synced.

**Disadvantages**

* You have to deal with your encryption keys securely.
* No audit logs. Your .git repos will be cloned by multiple developers, applications or servers. Soon your secrets will spread your organization and you will lose track of where they are and who has acces to them.
* No groups that let you easily specifiy permissions for multiple users.
* Hard to rotate an access. Rotating an access implies to revoke the key and redistribute it. The distribution part is not easy to handle with git repositories.

### Use a "secrets as a service" solution

There are secrets management services like [Hashicorp's Vault](https://www.vaultproject.io/) or [Square's Keywhiz](https://square.github.io/keywhiz/).

**Advantages**

Centralization really has a few advantages:
* It prevents secrets sprawl.
* It provides audit logs.

**Disadvantages**

The main disadvantage of these solutions is the cost of adoption. Their overhead is significant:
* As they introduce a single point of failure, they must be hosted on a highly-available and secure infrastructure.
* All the codebase must be changed.
* Keys giving access to the system must be carefully protected.

## Use temporary credentials

Some API providers make it possible to issue credentials with an expiration date.

## Whitelist your IPs
For some API providers you can add an extra layer of security by whitelisting your organization's IP addresses.

## Restrict permissions associated with your keys

It is very rare to have to generate root access keys (with full permissions). Some API providers allow granular permissions to their endpoints, enable read / write splitting or role-based access control.

A rule of thumb : accesses should be granted on a need-to-know basis only.

## Protect your development / testing secrets as well as your production secrets

This is a common mistake. The line is not clear between development, testing and production secrets. Production secrets can end up in development / testing and vice versa.

## Don't share secrets over emails, Slack, Skype, etc.

Yes, these services were designed to keep your conversations private. No, they were not designed to store your secrets in plain text.

Ever had one of these long email conversations forwarded to you with information that was not meant to be shared buried in the thread ?

Don't encourage copy-pasting secrets in multiple places. This increases the surface area for hacks.

## Use GitHub scanning tools

Keep in mind that why the good development practices exposed above help you reduce the risk of leak, none of these actually prevent your secrets from being pushed to GitHub. Because good development practices are sometimes ignored.

We are the authors of a GitHub scanning tool called [GitGuardian](https://www.gitguardian.com/?ref=github).
