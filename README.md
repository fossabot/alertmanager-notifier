# alertmanager-telegram-bot


[![pipeline status](https://gitlab.com/ix.ai/alertmanager-telegram-bot/badges/latest/pipeline.svg)](https://gitlab.com/ix.ai/alertmanager-telegram-bot/commits/latest)

A simple webserver in Flask, that translates alertmanager alerts into telegram messages.

**WARNING!** This is for testing only. It is not considered production ready!

## Running a simple test
```
docker run --rm -it \
    -p 9999:9999 \
    -e TELEGRAM_TOKEN="your token" \
    -e TELEGRAM_CHAT_ID="your chat id" \
    -e PORT=9999 \
    -e GELF_HOST=graylog \
    --name alertmanager-telegram-bot \
    registry.gitlab.com/ix.ai/alertmanager-telegram-bot:latest
```

Run the test agains the bot:
```
curl -X POST -d '{"alerts": [{"status":"Testing alertmanager-telegram-bot", "labels":[], "annotations":[], "generatorURL": "http://localhost"}]}' -H "Content-Type: application/json" localhost:9119/alert
```

## Configure alertmanager
```
route:
  receiver: 'telegram alerts'
  routes:
    - receiver: 'telegram-webhook'

receivers:
  - name: 'telegram-webhook'
    webhook_configs:
      - url: http://alertmanager-telegram-bot:9119/alert
```

## Supported environment variables:

* `TELEGRAM_TOKEN` (no default - **mandatory**) - see the [Telegram documentation](https://core.telegram.org/bots#creating-a-new-bot) how to get a new token
* `TELEGRAM_CHAT_ID` (no default - **mandatory**) - see this question on [stackoverflow](https://stackoverflow.com/questions/32423837/telegram-bot-how-to-get-a-group-chat-id)
* `GELF_HOST` (no default) - if set, the exporter will also log to this [GELF](https://docs.graylog.org/en/3.0/pages/gelf.html) capable host on UDP
* `GELF_PORT` (defaults to `12201`) - the port to use for GELF logging
* `LOGLEVEL` (default: `INFO`)
* `PORT` (default: 9119) - the port for the bot
