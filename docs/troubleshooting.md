# Troubleshooting

| Problem | Fix |
| --- | --- |
| `cloudflared: command not found` | Install cloudflared (see [Set Up SSH Access](connecting/index.md#install-cloudflared)) |
| Browser says "Forbidden" | Your email isn't on the access list — contact admin |
| `Permission denied (publickey)` | SSH key not deployed — send your `.pub` to admin |
| `websocket: bad handshake` | Run `cloudflared access login hpc.tp-stack.co.uk`, then retry `ssh YOUR_USERNAME@hpc.tp-stack.co.uk` |
| Can't see `/projects/` | Group membership not synced yet — run `id`, contact admin if missing |
| Job stuck in `PENDING` | Run `squeue --me`, check `REASON` — usually waiting for resources |
| "Module not found" in job | Activate your venv in the script: `source /projects/.../venv/bin/activate` |
| Portal won't load | Try incognito window or clear browser cache |
