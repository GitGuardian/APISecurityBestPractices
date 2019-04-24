If you want to add a new API key to the documentation, add it to the [table of contents](./Leak%20Mitigation%20Checklist.md#2-advice-specific-to-a-key) and follow the template below.

The section for one API key must include a **Revoke a key** subsection. It may include a **Check for suspicious activity** subsection (only if there is a way to do so). 
 
See an example (Slack) below.

# API key template

## <img src="icons/slack-logo.jpg" height="30" width="30" > Slack

### Revoke a key

Slack tokens are very convenient because they have the power to revoke themselves thanks to the [auth.revoke API method](https://api.slack.com/methods/auth.revoke)! Easy as a command line:

```
curl "https://slack.com/api/auth.revoke?token=xoxp-YOUR-TOKEN-HERE"
```

### Check for suspicious activity

There is an [audit logs api](https://api.slack.com/docs/audit-logs-api) which is only available to [Enterprise Grid](https://api.slack.com/enterprise-grid) customers.
