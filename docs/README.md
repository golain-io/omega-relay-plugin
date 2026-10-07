# Use Omega Relay in a chat

Connect an assistant to your computer. What it can do is what you allow: Files, Terminal, Browser, Screen & control, Agents, and capabilities you add yourself.

## Connect

1. On the computer, run `curl -fsSL https://bin.omegarelay.dev/install.sh | bash` and approve the computer in your browser. The app runs on Apple Silicon Macs with macOS 13 or later.
2. Add Omega Relay in your assistant. If it asks for a URL, use `https://omegarelay.dev/mcp`.
3. When the assistant sends you to Omega Relay, sign in and choose Full access or the capabilities you want, for each computer.

Two settings decide what an assistant can do. The Omega Relay app on the computer decides what that computer allows at all. The choice you make when an assistant connects decides what that assistant gets. An assistant never gets more than the computer allows.

## Try a task

- "Show the computers I connected and what you can do on each."
- "Find the README in my project folder and summarize what it says."
- "Run the tests in my project folder and show me what failed."
- "Open the pricing page in my browser and tell me what changed."
- "Look at the window in front and tell me what the error dialog says."
- "Hand this refactor to Claude Code in my project folder and tell me when it is done."

## Stop or change access

Change what an assistant may do, disconnect it from one computer, or remove it, in [Assistants](https://omegarelay.dev/connections). Pause every computer at once in [Computers](https://omegarelay.dev/dashboard). Pausing and disconnecting stop new calls. They do not cancel work that is already running; ask the assistant to cancel it, or stop it on the computer. Removing this package from your assistant does not remove the assistant's access.

## How the assistant works

The [Omega Relay skill](../skills/omega-relay/SKILL.md) tells an assistant how to find what it has and use it, and its [troubleshooting reference](../skills/omega-relay/references/troubleshooting.md) explains each error. The [extend skill](../skills/omega-relay-extend/SKILL.md) covers capabilities you add yourself: what Relay requires of them, how they are approved, and that only you can remove them.

Terminal commands and agent tasks run with your normal user permissions. Browser and screen tools act in your signed-in accounts. A screenshot is sent to the assistant; whether you see it depends on your chat client.

## Data

Each call and its result pass through Omega Relay and its device platform between your computer and the assistant you connected. Omega Relay's own activity record holds the operation's name, status, request id and time, and no arguments or results. See the [privacy page](https://omegarelay.dev/privacy). Help: [omegarelay.dev/support](https://omegarelay.dev/support) or support@golain.io.
