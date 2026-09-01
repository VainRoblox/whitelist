# whitelist

`PlayerWhitelist.json` is read by every Vain client, roughly every ten seconds, straight
off the raw CDN. Nothing authenticates it beyond the fact that only we can push here, so
**write access to this repo is the whole security model** — guard it the way you would a
signing key.

## Shape

| Field | |
|---|---|
| `WhitelistedUsers` | keyed by Discord id. `hash` is `sha512(robloxUserId .. "SelfReport")`, hex. |
| `BlacklistedUsers` | keyed by **plaintext** Roblox user id, value is the kick reason. |
| `Announcement` | `text`, `expiretime` (unix seconds), `targets` (`"all"` or comma separated user ids). |
| `KillVain` | `true` makes every client uninject on its next check. |

A user entry:

```json
"123456789012345678": {
  "hash": "<128 hex chars>",
  "level": 3,
  "attackable": false,
  "tags": [{ "text": "OWNER", "color": [255, 0, 0] }]
}
```

`level` is authority: a command in chat is obeyed only by clients whose own level is
**lower** than the sender's. `attackable: false` keeps the user off other members'
aimbots, unless the aimbot's owner ranks at or above them.

## Two things worth knowing

Only `WhitelistedUsers` is hashed. The blacklist and announcements sit here in plain
text, in a public repo — assume anyone can read who you have banned and why.

Hashing the user id and not the name is deliberate. Folding the display name in, as Vape
does, means anyone who changes their Roblox username silently stops matching and drops
off the list with no indication why.
