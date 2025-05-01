# Deploy a Discord Webhook [Alerter](https://komo.do/docs/resources#alerter)

Part of the [*Komodo Hub* collection.](https://github.com/komodo-hub/komodo-hub)

Deploys an [Alerter](https://komo.do/docs/resources#alerter) that pushes to a [Discord Webhook](https://discordjs.guide/popular-topics/webhooks.html#what-is-a-webhook)
. Docker image built from [foxxmd/komodo-utilities](https://github.com/FoxxMD/komodo-utilities).

## Requirements

* A [Webhook URL](https://discordjs.guide/popular-topics/webhooks.html#creating-webhooks-through-server-settings) for the channel you wish to push to

## Deploying

Use **either** of the methods below.

### Komodo Resource TOML

<details>

Create a [Resource Sync](https://komo.do/docs/sync-resources) with the TOML configuration below to

* create the [Stack](https://komo.do/docs/resources#stack) to run deploy-discord-alerter
* add the Discord Webhook [Variable](https://komo.do/docs/variables) and
* setup the [Alerter](https://komo.do/docs/resources#alerter) + configuration

Steps:

* Open Komodo Dashboard -> Syncs -> **New Resource Sync**
* Choose Mode -> UI Defined
  * Toggle the following to active:
    * Managed
    * Include Sync Resources
    * Include Sync Variables

Add the below configuration to **Resource File** field and then modify variables for your environment (DISCORD_WEBHOOK, endpoint.params.url, etc...)

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

  ## Delay alerts with below types for X milliseconds 
  ## and cancel pushing alert if it is resolved within that time
  #UNRESOLVED_TIMEOUT_TYPES=ServerCpu,ServerMem
  #UNRESOLVED_TIMEOUT=2000
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

**Save** the sync and then **Execute Sync** to create the Alerter.

</details>

### Manual Setup

<details>

Create a new [**Stack**](https://komo.do/docs/resources#stack) with the following for `compose.yaml` file.

```yaml
services:
  komodo-discord-alerter:
    image: foxxmd/komodo-discord-alerter:latest
    restart: unless-stopped
    env_file:
      - ./.env
    ports:
      - "7000:7000"
```

Add the following to the Stack -> Config -> Environment section:

```ini
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

## Delay alerts with below types for X milliseconds 
## and cancel pushing alert if it is resolved within that time
#UNRESOLVED_TIMEOUT_TYPES=ServerCpu,ServerMem
#UNRESOLVED_TIMEOUT=2000
```

Make sure to replace placeholder values. `[[DISCORD_WEBHOOK]]` is a Komodo [Variable](https://komo.do/docs/variables).

After deploying the Stack create a new [Alerter](https://komo.do/docs/resources#alerter)

* **Endpoint:** `Custom`
  * In the Endpoint field set the IP:PORT of the `komodo-discord-alerter` stack you created IE `http://192.168.YOUR.IP:7000`
* Optionally, set any **Alert Types** you may need

**Save** the Alerter and then **Test Alerter** to make sure everything is ready to use.

</details>