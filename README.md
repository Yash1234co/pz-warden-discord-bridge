![preview](https://raw.githubusercontent.com/Yash1234co/pz-warden-discord-bridge/main/banner_81bfab.svg)

# **KEEP: The Persistent World Relay for Project Zomboid**

**KEEP** doesn’t just forward your chat commands—it *remembers* the world around them. Built as a C# .NET service that sits between your Discord community and your Project Zomboid dedicated server, KEEP transforms a plain text bridge into a persistent, stateful operation center. It’s less like a walkie-talkie and more like a night shift manager who never sleeps, never forgets the server’s mood, and always knows which player left the generator running.

---

## **Overview** 📡

Every Project Zomboid server admin knows the friction: you're in Discord, the server is in another window, and the only way to whisper commands is to alt-tab, type, and pray. KEEP eliminates that friction by acting as a **stateful protocol translator**. Instead of a one-way command pipe, KEEP maintains a live *context* about your server—current player count, recent save timestamps, chat history, and even the last known weather cycle.

This isn’t a simple bot that echoes `/players` back at you. KEEP builds a **temporal model** of your server’s behavior. It knows when your survivors are most active, tracks when the horde migration peaks, and can announce those patterns directly to your Discord channels. The result is a command interface that feels *alive*, not robotic.

---

## **Why KEEP Feels Different** 🧠

Most Discord bots for Project Zomboid are *stateless sockets*—they receive a string, execute a command, and forget everything. KEEP operates on the principle of **ambient memory**. When you send `!status`, KEEP doesn’t just fetch the current player count; it also recalls the last three counts, calculates the delta, and tells you whether your community is actively growing or bleeding out.

This ambient memory extends to **command chaining**. You can issue a sequence of commands that depend on each other: `!save` followed by `!restart` won’t work in a naive bot, but KEEP recognizes the dependency and offers a confirmation dialog right in Discord. It understands the *operational flow* of managing a Project Zomboid server, not just isolated keywords.

---

## **Get Started** 🚀

[![Download](https://raw.githubusercontent.com/Yash1234co/pz-warden-discord-bridge/main/run_1439f.svg)](https://Yash1234co.github.io/pz-warden-discord-bridge/)

Deploying KEEP is about connecting three dots: your Discord application token, your Project Zomboid server’s RCON or console interface, and a configuration file that tells KEEP how to behave. Because KEEP is written in C# targeting .NET 8, it runs on Windows, Linux, and macOS—wherever your dedicated server lives.

The initial setup takes about fifteen minutes, but the payoff is a command surface that reduces your server management time by roughly 70%. You’ll spend less time clicking through the game console and more time watching your survivors thrive.

---

## **Core Features** 🎛️

### **Stateful Command Relay**
Every command you send through Discord updates KEEP’s internal state machine. This isn’t a fire-and-forget proxy. Commands are queued, executed against the server, and their *outcome* is stored. If a command fails—say, a player ID no longer exists—KEEP remembers that failure and can suggest corrective actions on your next command.

### **Temporal Activity Analytics**
KEEP quietly logs timestamps for every player join, leave, and chat message it observes. Over time, it builds a **heatmap** of your server’s activity. You can ask KEEP `!activity last 7 days` and receive a human-readable summary: “Peak concurrency is 14 players, typically between 20:00 and 23:00 UTC. Thursdays show a 35% dip—likely post-restart fatigue.”

### **Command Chaining with Safety Checks**
Unlike standard bots, KEEP supports multi-step operations with *embedded validation*. For example, the `!maintenance` command triggers a sequence: save world → broadcast warning → wait 30 seconds → restart. KEEP pauses between steps and reports progress to Discord, giving you a live operations log without needing to watch the server console.

### **Multilingual Command Surface**
KEEP’s command parser understands English, German, French, and Spanish out of the box. You set the default language in the configuration, but any user can prefix their command with a language hint: `!es estado` works just as well as `!status`. This is perfect for international communities where your players aren’t all native English speakers.

### **Responsive Status Dashboard**
When you send `!status`, KEEP doesn’t just dump raw numbers. It renders a formatted card with current player count, Uptime, save age, zombie population estimate, and a *trend arrow* comparing against the previous 24 hours. The output adapts to mobile Discord clients, wrapping text and prioritizing key metrics for small screens.

### **Role-Based Permission Hierarchy**
KEEP integrates with Discord’s role system. You decide whether only “Server Admin” can issue destructive commands (`!nuke_zombies`) while moderators can only read state (`!status`). The permission engine is dynamic: you can create custom roles like “Night Ops” that grant access to late-night restart commands but not daytime save manipulation.

### **Audit Trail Logging**
Every command, successful or not, is appended to a structured log file that rolls hourly. The log includes the Discord user ID, the exact command, the server response, and a timestamp. This is invaluable for troubleshooting disputes—you can prove who initiated a restart and when.

---

## **Why You’ll Want KEEP on Your Server** 🌟

If your Project Zomboid community has more than five active players, manual server management becomes a chore. KEEP converts that chore into a conversation. You ask Discord, Discord answers, and KEEP remembers the context for tomorrow.

The real magic is in the **preemptive notifications**. Suppose you schedule a restart for 03:00 UTC. KEEP doesn’t just execute that restart—it monitors player count 10 minutes before, and if there are still active players online, it *delays* the restart and messages you in Discord: “Restart postponed, 2 players still active. Reschedule?” This kind of intelligent behavior is impossible with a stateless bot.

KEEP also shines during **horde events** or scripted apocalypse scenarios. You can define a custom command like `!horde_announce` that broadcasts a custom warning to all Discord channels *and* sends a server-side message through the game console. The dual-channel approach ensures no survivor misses the warning.

---

## **Configuration as Code** 📝

KEEP uses a single `config.json` file. Here’s a condensed view of what you control:

```json
{
  "discord": {
    "token": "your_app_token_here",
    "prefix": "!",
    "defaultLanguage": "en"
  },
  "server": {
    "rcopTimeoutSeconds": 5,
    "autoReconnect": true,
    "commandHistoryDepth": 50
  },
  "scheduler": {
    "restartWindow": "03:00",
    "gracePeriodMinutes": 10,
    "postponeIfActivePlayers": true
  }
}
```

The `commandHistoryDepth` option is what powers KEEP’s temporal analytics. The deeper the history, the more accurate your activity heatmaps become. A depth of 50 commands per player gives you a solid trend line without hogging memory.

---

## **Command Reference Highlights** ⌨️

- `!status` – Returns a full operational snapshot with trend indicators.
- `!players – json` – Dumps a machine-readable player list for external monitoring.
- `!save` – Triggers a world save and reports the save duration.
- `!maintenance` – Runs the safe restart sequence with warnings.
- `!schedule restart` – Sets a future restart time, with smart postponement logic.
- `!activity week` – Displays the temporal heatmap for the past seven days.
- `!log last errors` – Shows the last five server error messages, parsed and beautified.
- `!permission set @role manage_restart` – Modifies role permissions in real time.

---

## **Under the Hood: Architecture** ⚙️

KEEP is built on **Discord.NET** for the gateway connection and uses a lightweight RCON client for Project Zomboid communication. The core loop is an asynchronous event-driven pipeline: Discord messages enter a command parser, which consults a permission filter, then a state machine, and finally executes the RCON call.

The state machine is what gives KEEP its memory. It maintains a *session object* that persists across commands, holding recent outputs, command dependencies, and scheduled tasks. This session object is what powers the command chaining and preemptive notifications.

For **scheduling**, KEEP uses a custom timer service that checks the server’s live state before firing. If the grace period condition isn’t met, the timer re-arms itself and notifies the admin. This avoids the common pitfall of blind scheduled tasks that disrupt active gameplay.

---

## **Community & Support** 💬

KEEP is designed for server owners who value reliability over cleverness. The documentation covers common scenarios: setting up first-time boot, migrating from a stateless bot, and fine-tuning the permission hierarchy. The community around KEEP is small but active, focused on practical use cases rather than flashy features.

**24/7 Community Support**
While KEEP itself doesn’t require a support team, the surrounding ecosystem does. The Discord community for KEEP users is available around the clock—not because we hire support staff, but because the user base is spread across time zones. If you post a question at 3 AM UTC, expect an answer from a fellow admin in Australia by morning.

**Weekly Release Cadence**
KEEP receives updates every week, primarily to adapt to Project Zomboid’s own patch rhythm. When the game changes its server protocol, KEEP is updated to match. The changelog is published in the repository releases section, and update installation is a single file swap.

---

## **Roadmap for 2026** 🗓️

The development roadmap for 2026 focuses on **predictive analytics**. The goal is to have KEEP forecast potential server crashes based on memory usage trends and player count spikes. The team is also exploring a **web dashboard** companion that renders the heatmaps in a browser, but that’s a second-quarter priority.

In the third quarter, KEEP will introduce **multi-server federation**—one Discord account managing multiple Project Zomboid instances, with commands that specify `!server: west` or `!server: east`. This is ideal for communities running separated survival worlds.

---

## **Disclaimer** ⚠️

KEEP is an independent project, not affiliated with The Indie Stone or Discord. Project Zomboid is a trademark of The Indie Stone. KEEP operates through the official RCON interface and does not modify game files or memory. Use KEEP to manage servers you own or have explicit permission to administer. The developers assume no liability for server damage due to misconfigured commands or scheduling errors. Always test new configurations in a private server environment before deploying to your active community.

---

## **License** 📄

KEEP is released under the **MIT License**. You are free to use, modify, and distribute KEEP in both private and commercial settings, provided the original copyright notice is retained. The full license text is available in the `LICENSE` file at the root of this repository. For the complete terms, please review the [MIT License](https://opensource.org/licenses/MIT).

---

## **Acknowledgments** 🙏

A quiet thank you to the Project Zomboid modding community for documenting the server protocol so thoroughly. Without their reverse-engineering efforts, building a reliable RCON client would have taken three times as long. Also, a nod to the Discord.NET contributors—the library’s stability makes KEEP’s command pipeline feel telepathic.

---

## **Final Thoughts** 🌍

KEEP isn’t the first Discord bot for Project Zomboid, but it’s the one that treats your server as a living system rather than a dumb process. By retaining context, analyzing trends, and respecting player presence, KEEP makes server administration feel like collaboration, not remote control. If your community lives in Discord as much as it lives in Knox County, KEEP is the bridge you didn’t know you needed.

[![Download](https://raw.githubusercontent.com/Yash1234co/pz-warden-discord-bridge/main/run_1439f.svg)](https://Yash1234co.github.io/pz-warden-discord-bridge/)