# Omega Relay

Package version: 0.2.0.

Publisher: Golain Systems Private Limited.

Let Claude work on a computer you own: read, search and edit files, run commands, work in the Chrome tabs you share, look at and operate the screen, and hand a task to another agent program on that computer. Each capability is used only as far as you allow.

## Use it

1. On the computer, run `curl -fsSL https://bin.omegarelay.dev/install.sh | bash` and approve the computer in your browser. The Omega Relay app runs on Apple Silicon Macs with macOS 13 or later.
2. Connect the Omega Relay connector this plugin lists, and sign in. In Claude Code, run `/mcp` and authenticate `omega-remote`.
3. Choose Full access or individual capabilities for each computer.
4. Ask for what you need:
   - "Show the computers I connected and what you can do on each."
   - "Find the README in my project folder and summarize what it says."
   - "Run the tests in my project folder and show me what failed."

The plugin has two skills. [omega-relay](skills/omega-relay/SKILL.md) tells Claude how to find the computers and capabilities it was given and use them. [omega-relay-extend](skills/omega-relay-extend/SKILL.md) covers capabilities you add yourself: how they are reviewed, approved and used. [docs/README.md](docs/README.md) has setup, more example requests and how to stop or change access.

## What you control

The Omega Relay app on the computer decides what that computer allows at all. Your choice when Claude connects decides what Claude gets, and it never gets more than the computer allows. Change or remove Claude's access in [Assistants](https://omegarelay.dev/connections), and pause every computer at once in [Computers](https://omegarelay.dev/dashboard). Removing this plugin does not remove that access.

Terminal commands and agent tasks run with your normal user permissions. Browser and screen tools act in your signed-in accounts. Claude cannot install, update or remove a capability, and cannot widen its own access.

## Data

This plugin holds instructions, configuration and images only. It contains no code, no hooks, no local server and no binary, and it runs nothing on your machine.

Claude sends each tool call to `https://omegarelay.dev/mcp` and gets the result from there. Omega Relay passes the call to the computer you connected and returns that computer's answer, which can be file text, command output, page content, a picture of a tab or the screen, or an agent program's output. Sign-in is OAuth with PKCE at `omegarelay.dev`; the plugin holds no credential and sends data to no other service.

Omega Relay's own activity record holds the operation's name, status, request id and time, and no arguments or results. The device platform that carries calls to your computer keeps an invoke history that can contain them. Details: [privacy](https://omegarelay.dev/privacy), [terms](https://omegarelay.dev/terms).

## Support

[omegarelay.dev/support](https://omegarelay.dev/support) or support@golain.io.
