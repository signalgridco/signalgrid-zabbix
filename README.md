# Signalgrid Zabbix

Send Zabbix alerts to Signalgrid push notifications.

This repo contains one file:

- `media_signalgrid.xml`

## What you need

- Zabbix with **Webhook** media types (this file is exported for Zabbix 7.0)
- A Signalgrid account
- Your Signalgrid **Client Key** and **Channel** token/ID

## Install

### 1) Import the media type
1. Download `media_signalgrid.xml`
2. In Zabbix: **Alerts → Media types**
3. Click **Import** and pick the XML file

You should now see a media type called **Signalgrid**.

### 2) Set the channel macro
Create this macro and set it to your Signalgrid channel token/ID:

- `{$SIGNALGRID_CHANNEL}`

Where you put it is up to you:
- Global: **Administration → General → Macros**
- Per host: **Data collection → Hosts → (host) → Macros**
- Per template: **Configuration → Templates → (template) → Macros**

### 3) Add it to a user (Client Key goes in “Send to”)
1. Go to **Users → Users**
2. Open the user
3. Add **Media**:
   - **Type:** Signalgrid
   - **Send to:** your Signalgrid **Client Key**
   - **Enabled**
4. Save

### 4) Use it in an action
1. Go to **Alerts → Actions**
2. Add an operation that sends a message to that user (or user group)

## How it sends

It POSTs to:

- `https://api.signalgrid.co/v1/push`

It sends:

- `client_key` (from the user media “Send to”)
- `channel` (from `{$SIGNALGRID_CHANNEL}`)
- `title`, `body`
- `type`, `critical`

## Severity mapping

- Recovery (`{EVENT.VALUE} == 0`) → `SUCCESS`
- Otherwise:
  - severity 5+ → `CRIT` + `critical=true`
  - severity 4 → `CRIT`
  - severity 2–3 → `WARN`
  - severity 0–1 → `INFO`

## Troubleshooting

- **client_key missing** → put your Client Key in the user media **Send to**
- **channel missing** → set `{$SIGNALGRID_CHANNEL}`
- **not sending** → check outbound HTTPS from Zabbix + your key/channel values
