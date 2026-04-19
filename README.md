# claude-window-keeper

Keeps your Claude Code 5-hour usage window always open by pinging `claude -p` a minute after each window resets.

## Why

Claude Code gives you 5 hours of usage per "window" — but the clock only starts ticking when you send your first message. It doesn't run while you're away.

So if you sit down cold and work, you get exactly 5 hours and then you're done.

If instead something was pinging Claude every 5 hours in the background — right as each window expires — there would always be a window *already running* when you arrive. You get whatever's left of the current one — on average ~2.5 hours — *and then* a fresh 5-hour window when it rolls over.

**~7.5 hours of work time instead of 5.**

## How it works

Each ping uses `claude -p --output-format stream-json`, whose response includes a `rate_limit_event` carrying the exact unix timestamp at which the current 5-hour window resets — the same value shown as "Resets in …" in the Claude Code UI. The script sleeps until `resetsAt + 1 minute` and pings again, so the next ping lands just after the window rolls over. The keepalive itself is the probe — no extra API call.

## What it costs

A one-line message, ~5 times a day. Insignificant token usage.

## Setup

```sh
git clone https://github.com/lspahija/claude-window-keeper.git
cd claude-window-keeper
chmod +x claude-window-keeper
./claude-window-keeper
```

## Run in the background

It's a plain shell script, so any of your usual tools work. The simplest:

```sh
nohup ./claude-window-keeper >> ~/keeper.log 2>&1 &
# watch it live:
tail -f ~/keeper.log
# stop:
pkill -f claude-window-keeper
```

For `systemd`, `launchd`, `tmux`, etc. — use whatever you normally use to run long-lived scripts.

## License

MIT.
