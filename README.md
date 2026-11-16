# Swing game permissions

`permissions.json` says who may use the admin console (the `;` key in game) and what each role may run. The game servers read it from this repository.

## Give someone a role

1. Find their Roblox user id: it is the number in their profile link, `roblox.com/users/<the id>/profile`.
2. Open `permissions.json`, click the pencil, and add the id to the list of the role, with a comma between ids:

   ```json
   "moderator": [123456, 7891011],
   ```

3. Commit the change.

To take a role away, delete the id from the list and commit.

Running servers pick the change up within about 10 minutes (GitHub keeps the old file for about 5 minutes, and each server reads it every 5). Nobody has to rejoin.

## Roles

- `admin`: every command.
- `moderator`: only the commands in `commands.moderator`.
- `tester`: only the commands in `commands.tester`.

Someone listed under several roles gets the highest one (admin, then moderator, then tester). A role does not get the commands of the roles below it: it runs exactly its own list.

The owner of the game's group, and every group rank from 254 up, are always admins, whatever this file says.

`kick` and `ban` never work on someone with a higher role: a moderator cannot remove an admin.

Anyone who can commit to this repository can make themselves an admin in the game, so keep write access to yourself.

## Change what a role may run

Edit that role's list under `commands`. The names are the console's command names, as `help` shows them to an admin. A name that is not a command is skipped. If a role has no list here, the game uses its built-in one.

## If the file has a mistake

A file the game cannot read is ignored as a whole, and the servers keep using the last good one, so check the commit after editing. These make the file unreadable:

- broken JSON (a missing comma or bracket, a comma after the last item)
- an id that is not a plain whole number (no quotes around ids)
- a role name other than `admin`, `moderator` or `tester`
- a list longer than 500 entries

A server that started while the file was unreadable uses the lists written in the game's Console script instead.

# Shop codes

`codes.json` holds the codes players type in the shop's Codes tab. Each code pays cash once per player.

```json
{
	"codes": {
		"WELCOME": { "cash": 2500 },
		"HALLOWEEN": { "cash": 10000, "expires": "2026-11-30" }
	}
}
```

- The name is the code. Capitals and spaces do not matter: players can type `welcome`.
- `cash` is what it pays.
- `expires` is optional. A date alone (`2026-11-30`) lasts until the end of that day, UTC. A full time also works: `2026-11-30T18:00:00Z`.

What a player sees:

- a code that is not in the file: no such code
- a code past its `expires`: it has expired (even if they used it before)
- a code they already used that has not expired: already used

To end a code early, set `expires` to a past date instead of deleting it, so players are told it expired. Deleting it makes it "no such code".

Servers read the file every 5 minutes, the same as permissions. A file with a mistake (broken JSON, `cash` that is not a number, a date that cannot be read, a name longer than 32 letters) is ignored whole and the last good one stays.
