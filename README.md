# Palworld dedicated server: Set one up without Linux skills, and size the RAM for your actual player count

Most people land on "Palworld dedicated server" for one of two reasons. Either the friend who was hosting has closed the game and taken everyone's base with him, or the four-player co-op cap has become a problem. Both are solved by renting a dedicated server. Neither is solved by renting the cheapest one you can find.

Two decisions decide whether your server is still alive in week three: how much RAM you're actually paying for, and how much of the setup you want to do by hand. Everything else — the host, the panel, the config file — follows from those.

## What a Palworld server actually needs

Pocketpair's own v1.0 documentation is the starting point: 4 CPU cores, 8 GB RAM minimum, 16 GB recommended, 32 GB for servers running 16+ players or heavy mods, roughly 12 GB of storage for server files, and Ubuntu 22.04 LTS if you're going Linux. The default port is UDP 8211. The hard player cap is 32.

The gap between "minimum" and "actually fine" is where people get burned. The 8 GB figure will boot a server. Pocketpair's docs also warn the same 8 GB carries a real risk of out-of-memory crashes, and host guides that have watched these servers in production say roughly the same thing: a quiet server at launch looks cheap, and the same server four weeks later — breeding pens, Pal boxes, sprawling bases — does not.

Here's the sizing picture the various guides converge on:

| Players | RAM the guides agree on | What happens in practice |
| --- | --- | --- |
| 1–4 | 8 GB minimum, 12–16 GB comfortable | Boots on 8 GB; world generation alone has been measured spiking a server from a 2–4 GB steady state up to 6–8 GB |
| 4–16 | 16 GB | Pocketpair's own recommended figure. This is the sane baseline for a real group |
| 16–32 | 32 GB | The 32-player cap is also a RAM figure; add more if you're running mods |

Three things matter more than the table.

**Memory is not purely a player-count problem.** A 10-player server with dense bases and active automation chains will use more memory than a 20-player world where everyone stays mobile. Session length matters too — the longer the process runs, the more it holds.

**Palworld leaks memory.** This is long-standing and well documented. Left alone, the server process steadily claims more RAM over a session, which is why a nightly restart is the standard workaround rather than a paranoid habit. On a Linux box that's one cron line; on a Windows VPS it's a scheduled task or a manual habit.

**Single-core speed beats core count.** Pal AI, base simulation and automation appear to be largely single-threaded. A modern 4-core chip at a high clock will usually outrun a slower 8-core in this game. That's why game hosts advertise high-frequency CPUs — and it's the correct thing to shop for.

## Three ways to run a Palworld server, in order of how much you have to learn

There's no single "right" setup here, but there's a right setup for your patience level.

**One-click game panel.** You buy a plan, choose Palworld from a game list, and the server initialises itself in a few minutes. The panel shows you an address and port, plus a file manager and a visual editor for `PalWorldSettings.ini`. No SSH, no SteamCMD, no Linux. This is the route that eliminates the setup tutorial entirely.

**A Windows VPS with Palworld preinstalled.** You pick Windows Server, tick the Palworld preinstall option, connect over remote desktop, and double-click a batch file on the desktop. Players connect to `your-IP:8211`. The config file sits at `C:\PalServer\Pal\Saved\Config\WindowsServer\PalWorldSettings.ini`. The reason to prefer Windows isn't performance — it's mods. Script-based mods that rely on UE4SS don't run on Linux dedicated servers, so a Windows box is the only straightforward path if modding is on your roadmap.

**Manual SteamCMD on Linux.** Full control, and the standard approach when you rent a plain VPS anywhere. You install 32-bit GCC runtime libraries, install SteamCMD, pull the server with `steamcmd +login anonymous +app_update 2394010 validate +quit`, and keep it alive with `screen` or `tmux`. Cheapest and most flexible, and also the route where you'll be reading logs at 1 a.m.

RainYun's documentation covers all three, which is unusual — most hosts document one and leave you to work out the rest. The panel route uses their in-house game panel, and the finished server exposes both a file manager and a Palworld management panel so you can restart the process, adjust settings and run backups without touching a terminal. There's also a config file translation, a save-migration guide, and an unofficial server build that patches the memory leak and high CPU load if you'd rather fix the cause than restart nightly.

If one-click is what you're after: 👉 [👉 Open a RainYun panel server with Palworld selected](https://www.rainyun.com/MzA2MTc2_)

## What RainYun's game cloud plans look like

RainYun (雨云) is a Chinese cloud provider founded in 2018. Its game cloud product line is split into three purchase types — VPS, the in-house RainYun Panel, and an MCSM panel — and the panel tier list published on the official game cloud page is straightforward:

| Plan (panel) | CPU / RAM | Bandwidth | Storage | Price | Billing | Buy |
| --- | --- | --- | --- | --- | --- | --- |
| 入门级 Entry | 2 cores / 4 GB | 10 Mbps up, 200 Mbps down | 30 GB + 20 GB | from ¥125/month | Monthly or annual (30% off annual) | [ Buy the Entry panel plan](https://www.rainyun.com/MzA2MTc2_) |
| 进阶级 Standard | 4 cores / 8 GB | 15 Mbps up, 200 Mbps down | 30 GB + 20 GB | from ¥220/month | Monthly or annual (30% off annual) | [ Buy the Standard panel plan](https://www.rainyun.com/MzA2MTc2_) |
| 超高端 High | 6 cores / 12 GB | 20 Mbps up, 200 Mbps down | 30 GB + 20 GB | from ¥360/month | Monthly or annual (30% off annual) | [ Buy the High panel plan](https://www.rainyun.com/MzA2MTc2_) |

A few honest caveats, because "from" is doing work in that table. Prices are quoted in CNY and vary with the CPU machine type and the datacenter you pick; the player-count figures RainYun prints next to each tier are Minecraft reference numbers, not Palworld ones, so treat the RAM column as the real guide. The VPS purchase type is priced differently again and its machine lineup has included i9-14900KF, i9-13900KF, Ryzen 7950X, Ryzen 5900X, Xeon Gold 6146 and E5-2666v3, with stock rotating by region.

Two billing modes matter for cost. Fixed billing is a flat monthly rate with no extra charges — the right choice if the server runs continuously. Dynamic billing has a lower base and bills against CPU usage, which is cheaper if you only play a couple of hours a day.

The discounts are real but need verifying at checkout. RainYun's own setup docs state that annual billing takes 30% off, and that registering through a referral code and binding WeChat gets you a 50% first-month coupon. Bloggers tracking the affiliate program report a points-mall 20% coupon that stacks with the annual discount, landing around 5.6折 — roughly 44% off list. That stacking is affiliate-reported rather than something I could confirm on the official pricing page, so check what's actually available in your account before you commit. The WeChat requirement is worth flagging: if you don't have a WeChat account, plan on paying list price.

On risk: game cloud and cloud server products can be trialled for ¥1 for one day, and there's a 7-day no-reason refund policy, limited to three uses per account. For a game server you're not sure about, that's a cheaper experiment than a month's commitment.

👉 [👉 Check live RainYun game cloud pricing and regional stock](https://www.rainyun.com/MzA2MTc2_)

## Panel or VPS: the tradeoff that isn't in the marketing table

The panel wins on effort. You're not managing an operating system, updates are handled, and the CPU machine type can be swapped without rebuilding. It's also the option where the RAM ceiling is baked into the tier you buy.

The VPS wins on control. You choose the OS, install whatever you like, run multiple server instances, and manage ports yourself. You also take on patching and firewall configuration.

Two details trip people up on the VPS side specifically.

**NAT versus dedicated IP.** Cheaper game cloud plans often share a public IP, and you forward ports yourself. RainYun's NAT game plans allow around 15 port mappings, and Palworld needs UDP 8211 among them. If you don't forward it, nobody can join and the panel will look perfectly healthy. Dedicated IPs cost extra — historically somewhere in the region of ¥30–200 per month depending on region and DDoS protection level, which is enough to change the maths on a cheap plan.

**Windows firewall.** RainYun's Windows Server 2019 image ships with the firewall off, and their guide leaves it that way for simplicity. If you'd rather not run without it, open 8211/UDP (plus 8211/TCP if you want to be thorough) and leave the rest closed.

Both routes give you the same three admin surfaces: the game panel for start/stop, the file manager for `PalWorldSettings.ini`, and RCON or chat commands if you enable them. Beyond that, the settings that affect stability most are `ServerPlayerMaxNum` (cap 32) and `BaseCampWorkerMaxNum` (default 15, and one of the larger contributors to CPU and memory load — many guides suggest 10–12 on busier servers).

## Region and latency, honestly

RainYun is a mainland-China provider. Its game cloud datacenters have included Jiangsu (Suqian, Zhenjiang), Zhejiang (Ningbo, Wenzhou, Shaoxing) and Hubei, with stock shifting between them. The wider cloud lineup also covers Hong Kong, Japan, South Korea, Germany and the US, but the game cloud line — the high-frequency CPU machines, the BGP routes, the 100–200 Gbps DDoS filtering — is mostly aimed at mainland players.

That means the honest answer depends on where your group lives. Playing from Asia, a mainland datacenter gives you single-digit-to-30ms latency and the price is hard to argue with. Playing from North America or Europe, the same server is the wrong purchase no matter how good the specs look. Check latency before you pay, not after.

## Before you check out

- **RAM first.** 16 GB is the number for a real group. 8 GB boots and then tests your patience. 32 GB if you're pushing toward the player cap or modding.
- **Decide on mods now.** Script mods mean Windows. If you buy a Linux VPS and then discover you want them, you're rebuilding.
- **Know whether you have a dedicated IP.** NAT plans work fine, but you own the port mapping.
- **Set the restart.** Nightly, and before anyone blames the host for a crash that's a known leak.
- **Test the region.** Especially if your group isn't in Asia.
- **Use the trial.** ¥1 for a day is the cheapest way to find out whether a panel plan hits your player count before you buy a year.

## FAQ

**How much RAM does a Palworld server need?**
8 GB is the documented minimum and 16 GB is the documented recommendation. Anything above roughly 6 players, or any world with mature bases, should be on 16 GB or more. Servers running 16+ players or heavy mods are usually quoted at 32 GB.

**Can I run a Palworld server on a cheap 8 GB VPS?**
You can boot one. Whether it stays up is another matter: world generation has been measured spiking memory well above steady state, which is exactly when an 8 GB box falls over. If a small group and a nightly restart are acceptable, it can work. Otherwise, spend the extra.

**Does the Palworld dedicated server support mods?**
Yes, with a caveat. PAK mods work on both Windows and Linux; script-based mods that need UE4SS don't run on Linux dedicated servers. RainYun's documentation states plainly that mod installation is Windows-only on their platform.

**Is PVP available?**
Not natively. RainYun's own notes say that as of version 1.5.1 Pocketpair still doesn't support a PVP mode directly, and the workaround is enabling player-versus-player damage through server settings. Set it before the world fills up rather than after.

**How do players connect?**
They open Palworld, choose Join Multiplayer Game (Dedicated Server), and enter your address and port in the form `IP:8211`. If you're on a NAT plan, it's the mapped public port, not 8211 itself — that detail accounts for a large share of "why can't anyone join" threads.

👉 [👉 Start with a RainYun game cloud plan built for one-click Palworld hosting](https://www.rainyun.com/MzA2MTc2_)
