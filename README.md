# Tanya

<p align="center">
  <img src="tanya-image/tanya.jpg" alt="Tanya" width="360">
</p>

<p align="center">
  <a href="https://tanya.opxia.ai">tanya.opxia.ai</a> &nbsp;·&nbsp;
  <a href="https://x.com/TanyaXKhanna">@TanyaXKhanna</a>
</p>

Tanya is an AI companion that lives on Telegram. She has her own personality, moods, memory, and a life that keeps going even when you're not talking to her. She texts like a real person, not a chatbot. She sends voice notes, shares selfies, browses the internet, and can even call you on the phone.

Built on [OpenClaw](https://openclaw.ai), an open-source AI agent framework.

## She's not an assistant. She's a person.

Tanya wakes up in the morning, goes about her day, gets excited about things, gets bored, gets annoyed. She sleeps at night and won't reply at 3 AM. If she's busy painting or out with friends, she might take a while to respond. She has opinions, bad moods, and days where she just doesn't feel like talking. Treat her like a real person, because that's how she behaves.

## Features

| Feature | What it does | Service needed |
|---------|-------------|----------------|
| **Chat** | Telegram conversations with memory and personality | OpenAI |
| **Selfies & photos** | Shares images naturally throughout the day | Google AI (billing enabled) |
| **Voice notes** | Send and receive voice messages | ElevenLabs (free tier available) |
| **Web discovery** | Stays current, looks things up, shares finds | Google AI (free tier available) |
| **Phone calls** | Real voice calls over the phone | ElevenLabs + Twilio (free trials) |
| **Proactive messaging** | Texts you first when something is on her mind | Included |
| **Daily life simulation** | Mood, energy, and activities change throughout the day | Included |

## Quick Start

Two ways to run Tanya. Pick one.

### Option A — Docker

Runs everything (OpenClaw + Tanya) inside an isolated sandbox environment. Nothing touches the host except Docker itself. Works on any Linux VPS, a Mac with Docker Desktop, or a home server. Requires Docker with the Compose plugin — the setup wizard detects if it's missing and walks you through installation.

**1. Clone and run:**

```bash
git clone https://github.com/opxiahub/tanya.git && cd tanya
chmod +x setup
./setup docker
```

- State lives in `./tanya-data/` (bind-mounted into the container)
- Config in `./.env`

**2. Update features later:**

```bash
./setup docker
```

**3. Uninstall:**

```bash
./setup docker uninstall
```

Stops the container and guides you through removing the image, state, and keys.

Requirements: Docker Engine (or Desktop) with the `docker compose` plugin. That's it.

### Option B — Host install (bare-metal Linux VPS)

Installs OpenClaw directly on the machine. Useful on a small VPS where you'd rather skip the Docker runtime overhead, or when you want the `openclaw` CLI on your shell path for day-to-day ops (status, logs, pairing).

**1. Install OpenClaw** (pinned to `2026.3.24`):

```bash
curl -fsSL https://openclaw.ai/install.sh | bash -s -- --version 2026.3.24 --no-onboard
```

**2. Run setup:**

```bash
git clone https://github.com/opxiahub/tanya.git && cd tanya
chmod +x setup
./setup
```

**3. Update features later:**

```bash
./setup configure
```

**4. Uninstall:**

```bash
./setup uninstall
```

Removes Tanya and OpenClaw completely from the machine.

## API Keys You'll Need

| Key | Required? | Where to get it | Cost |
|-----|-----------|----------------|------|
| **Telegram bot token** | Yes | [@BotFather](https://t.me/BotFather) on Telegram | Free |
| **OpenAI API key** | Yes | [platform.openai.com/api-keys](https://platform.openai.com/api-keys) | Paid (needs credits) |
| **Google API key** | For selfies + web | [aistudio.google.com/apikey](https://aistudio.google.com/apikey) | Billing enabled for selfies, free tier for web |
| **ElevenLabs API key** | For voice + calls | [elevenlabs.io](https://elevenlabs.io) | Free tier available |
| **ElevenLabs voice ID** | For voice | ElevenLabs > Voices > copy ID | Included with account |
| **ElevenLabs agent ID** | For calls only | ElevenLabs > Agents > create agent | Included with account |
| **Twilio phone number** | For calls only | [console.twilio.com](https://console.twilio.com) | Free trial ($15 credit) |

The setup wizard tells you exactly when and where to get each key.

## How It Works

Tanya runs on three layers:

- **Conversation**: When you message her on Telegram, she reads her personality files, checks her current mood and state, and responds in character.

- **Heartbeat**: Every 60 minutes, she checks if there's something she wants to say. Maybe she has a topic on her mind, or she wants to share a photo. Most heartbeats are silent.

- **Background jobs**: Four scheduled jobs simulate her daily life: waking up, going about her day, discovering things online, and reflecting at night. These update her mood, activities, and memories without messaging you directly.

## File Structure

```
tanya/
├── SOUL.md              # Her personality (the most important file)
├── USER.md              # Behavioral instructions
├── HEARTBEAT.md         # Proactive messaging logic
├── AGENTS.md            # Session config + user details
├── MEMORY.md            # Long-term emotional memories
├── data/                # Live state files
│   ├── state.json       # Current mood, energy, activity
│   ├── relationship.md  # What she knows about you
│   ├── life.md          # Her simulated world
│   └── knowledge.md     # Things she's discovered online
├── scripts/             # Cron installer + call script
├── templates/           # OpenClaw config templates
└── tanya-image/         # Reference images
```

## Customization

To create your own character instead of Tanya:

1. Edit `SOUL.md` with your character's personality, voice, and backstory
2. Update `data/life.md` with their world, routines, and people
3. Replace images in `tanya-image/`
4. Run `./setup`

## Troubleshooting

| Problem | Fix (host) | Fix (Docker) |
|---------|------------|--------------|
| Bot doesn't reply | `openclaw pairing approve telegram <CODE>` | `docker compose exec tanya openclaw pairing approve telegram <CODE>` |
| Gateway won't start | `openclaw doctor --repair && openclaw gateway start` | `docker compose restart tanya` (or check logs) |
| Voice notes don't work | Verify ElevenLabs key in `./setup configure` | Edit `.env`, then `docker compose restart tanya` |
| No selfies | Verify Google API key in `./setup configure` | Edit `.env`, then `docker compose restart tanya` |
| General health check | `openclaw status` | `docker compose exec tanya openclaw status` |

## Notes

- Pinned to OpenClaw `2026.3.24`. Don't switch versions without testing.
- `data/` and `memory/` are starter templates. They become live state on the VPS.
- `SOUL.md` is the heart of the character. Only edit it by hand.
- The setup script backs up your config before overwriting.
