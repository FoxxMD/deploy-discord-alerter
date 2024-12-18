# Deploy a Discord Webhook [Alerter](https://komo.do/docs/resources#alerter)

Part of the [*Komodo Hub* collection.](https://github.com/komodo-hub/komodo-hub)

Deploys an [Alerter](https://komo.do/docs/resources#alerter) that pushes to a [Discord Webhook](https://discordjs.guide/popular-topics/webhooks.html#what-is-a-webhook)
. Docker image built from [foxxmd/komodo-utilities](https://github.com/FoxxMD/komodo-utilities).

## Requirements

* A [Webhook URL](https://discordjs.guide/popular-topics/webhooks.html#creating-webhooks-through-server-settings) for the channel you wish to push to

## Komodo Resource TOML

```toml
[[stack]]
name = "discord-alerter"
[stack.config]
repo = "foxxmd/deploy-discord-alerter"
file_paths = [
  "compose.yaml",
]
environment = """
  ## Required

  ## Your webhook URL
  DISCORD_WEBHOOK = [[DISCORD_WEBHOOK]]

  ## Optional

  ## Set whether to include Komodo Severity Level in notification title
  #LEVEL_IN_TITLE=true

  # Prefixes messages with a checkmark when the Alert is in the 'Resolved' state
  #INDICATE_RESOLVED=true

  # Filter if an alert is pushed based on its Resolved status
  # * leave unset to push all alerts
  # * otherwise, alerts will only be pushed if Alert is one of the comma-separated states set here
  #ALLOW_RESOLVED_TYPE=resolved,unresolved
"""

[[variable]]
name = "DISCORD_WEBHOOK"
value = "https://discord.com/api/webhooks/0977431246518568931/bvLFKe_eqSAPf2CjU2-ESYMNFhJ815AvtCI"
is_secret = false

[[alerter]]
name = "discord-webhook"
[alerter.config]
enabled = true
endpoint.type = "Custom"
endpoint.params.url = "http://discord-alerter-ip:7000"
```