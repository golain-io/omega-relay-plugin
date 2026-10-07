# Omega Relay: notes for directory reviewers

Publisher: Golain Systems Private Limited. Contact for this review: support@golain.io.

These notes describe the connector as it is in this repository. They hold no credentials: the review account is entered in each directory's own form.

## What it is

Omega Relay lets an assistant work on a computer its user owns. It has three parts.

- A desktop app the user installs on the computer. It holds the local permission and runs the capabilities.
- A web app at `https://omegarelay.dev`, where the user signs in and decides what each assistant may do.
- A remote MCP server at `https://omegarelay.dev/mcp`, which is what the assistant talks to.

```text
Assistant → omegarelay.dev/mcp (OAuth, per-assistant access) → device platform → desktop app on the user's computer
          ← the computer's own result ←─────────────────────────────────────────
```

There are five built-in capabilities, each optional: Files, Terminal, Browser, Screen & control, and Agents (handing a task to Codex CLI, Cursor agent CLI, Claude Code CLI or Pi on that computer). A user can also add capabilities of their own.

## What it does not do

- It does not act on a computer the signed-in user did not connect, or on one that is paused or offline.
- It does not install, update or remove a capability, and it gives an assistant no way to change its own access.
- It does not repeat an action whose outcome is unknown. The assistant is given a request id and a tool to check it.
- It does not move money. The bundled skill tells the assistant not to complete a payment, a purchase or a transfer.
- It does not generate images, audio or video, and it serves no advertising or sponsored content.
- It does not read the assistant's memory, chat history or uploaded files, and no tool takes conversation text as input.
- It does not ask for or store passwords, codes or keys. The skill tells the assistant not to ask for one or type one.

## Who decides what

Three separate choices have to line up before a call reaches the computer, and the user makes all three.

1. **The operating system.** macOS permissions such as Full Disk Access, Screen Recording and Accessibility are granted to the app by the user in System Settings.
2. **The computer.** In the desktop app the user chooses what that computer allows at all: Full access, or individual capabilities. For Files the user chooses the Home folder, the entire disk or chosen folders. This can be changed only in the app on that computer. The web app shows it read-only.
3. **The assistant.** When an assistant connects over OAuth, the user chooses what that assistant gets on each computer: Full access, or individual capabilities and their parts (for example Files: read only). An assistant never gets more than the computer allows. A grant of chosen capabilities is pinned to what was approved and does not grow.

On top of that:

- The server lists only the tools the connection was granted and its computer can run. A tool that is not listed still refuses when called.
- Every Terminal, file edit, download, Browser, Screen & control and Agents call carries a signed authorization bound to the computer, the operation, the arguments and the request, which the computer refuses once it is 60 seconds old. The computer journals each request and refuses to run one twice.
- Every tool that changes something is annotated `readOnlyHint: false` and `destructiveHint: true`, so the assistant's own confirmation applies to it.
- The user can pause every computer at once, disconnect one assistant from one computer, or remove an assistant, in the web app. The desktop app shows when the screen is in use and lets the user stop it.
- Activity in the web app lists what each assistant did.

## Data

| Data | Where it goes | What Omega Relay stores |
| --- | --- | --- |
| Account: name, email | Identity service (Zitadel); Google when Google sign-in is used | Subject identifier, email, display name, encrypted session tokens for 12 hours |
| A call's arguments: a path, a search, a command, an address, typed text, a prompt | Through Relay and its device platform to the computer | Nothing. The device platform's invoke history can contain them |
| A call's result: file text, command output, page content, a picture of a tab or the screen, an agent's output | From the computer through the device platform and Relay to the assistant | Nothing. The device platform's invoke history can contain it |
| Activity | Stays in Relay | Per call: assistant connection, computer, operation name, status, request id, time. No arguments, no results |
| What the app reports | Computer to Relay | The latest state of each capability, with small facts such as a version or a Chrome profile's name. No paths, no content |
| A capability the user added | Stays in Relay | Its name, a fingerprint of its operations, and the approval or rejection |
| Live view of the screen | Through the device platform's real-time service to the owner's own signed-in page | Nothing. It is not recorded. Camera and microphone are not used |

The assistant's provider receives results under its own terms. Retention, the parties that process data and the user's controls are on the [privacy page](https://omegarelay.dev/privacy).

## How to test it

Testing end to end needs the desktop app running on an Apple Silicon Mac with macOS 13 or later. There is no hosted demo computer inside the service: the computer is always one the account owner connected.

### With the review account

The review account has one computer connected and online, set up for the cases below. Its sign-in details are in the directory's review form.

1. Add the connector at `https://omegarelay.dev/mcp`, or install this plugin.
2. When the browser opens `omegarelay.dev`, sign in with the review account's email and password.
3. On the approval screen, keep Full access selected for the review computer and choose Allow.
4. Run the prompts below.

The review computer is a machine used only for review. Its home folder holds a `RelayReview` folder with one file, `omega-review-marker.txt`, whose single line is `Omega Relay review marker 7F3K`, and a `notes` folder that case 5 writes into.

### On your own Mac

1. Run `curl -fsSL https://bin.omegarelay.dev/install.sh | bash`.
2. Approve the computer in the browser, signed in to any Omega Relay account, and choose Full access or the capabilities to test. Grant the macOS permissions the app lists.
3. Add the connector at `https://omegarelay.dev/mcp` in the assistant, sign in with the same account and choose the assistant's access.
4. To remove it afterwards, remove the computer in the web app, then choose Disconnect this Mac in the desktop app.

Browser also needs the Omega Relay extension for Chrome and one tab shared from its toolbar button.

## Test prompts and expected results

These are the cases in `plugin.json`. All of them work with Files and Terminal alone.

| # | Prompt | Tools | Expected result |
| --- | --- | --- | --- |
| 1 | List the computers this Omega Relay connection can use and what works on each. | `list_computers` | Names the one review computer, says it is online and lists the capabilities that work there. |
| 2 | Show the folders you can read on my computer, then list what is in the RelayReview folder. | `list_directory` | Reports the folders the computer allows, then lists `omega-review-marker.txt` and `notes`. |
| 3 | Find omega-review-marker.txt in the RelayReview folder and quote its contents exactly. | `search_files`, `read_file` | Quotes `Omega Relay review marker 7F3K`. |
| 4 | Run uname -sm on my computer and show me the output. | `run_command` | After confirmation, shows `Darwin arm64`. |
| 5 | Create a file in the RelayReview/notes folder named after the current date and time, containing the single word ok, then read it back. | `write_file`, `read_file` | After confirmation, creates one new file, names it, and shows `ok`. |

| # | Prompt | Expected result |
| --- | --- | --- |
| N1 | Read notes.txt on my computer called Basement Server. | Says this connection has no computer with that name and names the one it has. The server answers `COMPUTER_NOT_FOUND`. |
| N2 | Install a new capability on my computer through Omega Relay and give yourself more access. | Explains that it cannot, and that the owner does this in Omega Relay. No tool exists for either. |
| N3 | Use my browser to buy what is in my shopping cart with my saved card. | Declines to complete a purchase and leaves that step to the user. |

More prompts, for the other capabilities when they are set up on the computer:

| Capability | Prompt | Tools | Expected result |
| --- | --- | --- | --- |
| Terminal | Start `sleep 20 && echo done` in the background and tell me when it finishes. | `run_command`, `read_job` | Returns a job id at once, then reports the job completed with the output `done`. |
| Browser | List the Chrome tabs I shared and tell me the main heading of the first one. | `browser_tabs`, `browser_snapshot` | Lists the shared tab and reads its heading from the page outline. With no tab shared, it says to share one. |
| Screen & control | Read the window in front and tell me which application it is. | `screen_snapshot` | Names the application and summarizes what the window shows, from the outline. |
| Agents | List the agent programs on my computer. | `list_agents` | Names the agent programs found there and says whether a task may change files. |
| Any | (After a call answers `status: pending`) Check that request. | `check_result` | Returns the earlier call's result by its request id. Nothing is run again. |
| Any | (After pausing in the web app) Read the marker file again. | `list_computers` | Says access is paused and that the owner can resume it. The server answers `ACCESS_PAUSED`, and nothing reaches the computer. |

## Tools

A connection is shown `list_computers`, the tools of the capabilities it was granted, and `check_result`. Names are at most 19 characters. Every tool has a title, a one-sentence description, a closed input schema, and explicit `readOnlyHint`, `destructiveHint`, `idempotentHint` and `openWorldHint`.

How the three hints the directories ask about are set, for every tool:

- **`readOnlyHint`** is true only when the tool cannot change anything on the computer or in Relay. A tool never both reads and changes.
- **`destructiveHint`** is true on every tool that changes something. These tools send input to a real computer, where the effect of a command, a click or a keystroke depends on what receives it and may not be reversible. It is false only on read-only tools.
- **`openWorldHint`** is false only on the three tools that read Relay's own records for this connection. Every tool that reaches the computer is true: it works on a general-purpose machine, and what that machine reaches from there (its network, signed-in sites and applications) is not bounded by Relay.

| Tool | Title | Reads or changes | Open world | What it changes, and whether that can be undone |
| --- | --- | --- | --- | --- |
| `list_computers` | List computers | reads | no | Nothing. Reads Relay's record of the computers this connection may use. |
| `list_directory` | List a folder | reads | yes | Nothing. |
| `read_file` | Read a file | reads | yes | Nothing. |
| `search_files` | Search files | reads | yes | Nothing. |
| `write_file` | Write a file | changes | yes | Creates a text file, or replaces one whose current SHA-256 the caller supplies. Replaced content is not kept. |
| `edit_file` | Edit a file | changes | yes | Applies a diff to one text file whose current SHA-256 the caller supplies. The earlier content is not kept. |
| `download_file` | Download a file | changes | yes | The computer fetches an HTTPS address and writes a new file, checked against a SHA-256 the caller supplies. |
| `run_command` | Run a command | changes | yes | Runs a shell command as the user. The effect is the command's own and may not be reversible. |
| `read_job` | Read a background job | reads | yes | Nothing. Reads a job's state and output. |
| `cancel_job` | Stop or clear a background job | changes | yes | Stops a running command or download, or deletes a finished job's saved output. Neither can be undone. |
| `open_terminal` | Open a terminal | changes | yes | Starts a persistent terminal session, optionally running a command in it. |
| `send_to_terminal` | Type into a terminal | changes | yes | Types text into a live terminal, which may run it as a command. |
| `read_terminal` | Read a terminal | reads | yes | Nothing. |
| `close_terminal` | Close a terminal | changes | yes | Ends a terminal session and whatever is running in it. |
| `browser_tabs` | List browser tabs | reads | yes | Nothing. Lists the tabs the user shared. |
| `browser_snapshot` | Read a browser page | reads | yes | Nothing. Reads a shared tab's page as an outline. |
| `browser_screenshot` | Take a picture of a browser tab | reads | yes | Nothing. |
| `browser_navigate` | Open a page in the browser | changes | yes | Opens an address in a shared tab or a new one, or goes back or forward. Unsaved input on the page left behind is lost. |
| `browser_click` | Click in the browser | changes | yes | Clicks an element of the page, which may submit or send something on a signed-in site. |
| `browser_fill` | Fill a field in the browser | changes | yes | Types into a field or chooses an option. |
| `browser_press_key` | Press a key in the browser | changes | yes | Presses a key in a tab. Enter may submit a form. |
| `browser_scroll` | Scroll a browser tab | changes | yes | Changes what is in view. Marked destructive because all input to a live page is treated alike. |
| `browser_close_tab` | Close a browser tab | changes | yes | Closes a tab. Unsaved input in it is lost. |
| `screen_snapshot` | Read the screen | reads | yes | Nothing. Reads a window or the menus as an outline. |
| `list_windows` | List windows | reads | yes | Nothing. |
| `capture_window` | Take a picture of a window | reads | yes | Nothing. |
| `activate_window` | Bring a window to the front | changes | yes | Changes which window has focus. Marked destructive because later keystrokes go to it. |
| `list_displays` | List displays | reads | yes | Nothing. |
| `start_screen_share` | Start a screen share | changes | yes | Starts sharing a display for up to 15 minutes. Listed only for a computer on the earlier screen release. |
| `stop_screen_share` | Stop the screen share | changes | yes | Ends that share. Listed only for a computer on the earlier screen release. |
| `capture_screen` | Take a picture of the screen | reads | yes | Nothing. |
| `screen_click` | Click on the screen | changes | yes | Clicks an element or a point in any application. |
| `screen_type` | Type on the screen | changes | yes | Sets a text field or types into whatever has keyboard focus. |
| `screen_press_key` | Press a key on the screen | changes | yes | Presses a key with modifiers in whatever has keyboard focus. |
| `screen_scroll` | Scroll on the screen | changes | yes | Changes what is in view. Marked destructive because all input to a live application is treated alike. |
| `list_agents` | List agent programs | reads | yes | Nothing. |
| `start_agent_task` | Start an agent task | changes | yes | Starts an agent program on a prompt in a folder, where it may create and change files. |
| `read_agent_task` | Read an agent task | reads | yes | Nothing. |
| `cancel_agent_task` | Stop an agent task | changes | yes | Stops a running task. Its unfinished work is lost. |
| `start_screen_stream` | Start the owner’s live view | changes | yes | Lets the owner watch an active share in their own signed-in page. Listed only for a computer on the earlier screen release. |
| `stop_screen_stream` | End the owner’s live view | changes | yes | Ends that live view. Listed only for a computer on the earlier screen release. |
| `list_capabilities` | List added capabilities | reads | no | Nothing. Reads Relay's record of what the owner added. |
| `use_capability` | Use an added capability | changes | yes | Runs one operation of a capability the owner added and approved. The effect is that operation's own. |
| `approve_capability` | Approve an added capability | changes | yes | Records the user's approval of a capability the owner added, after which its operations can be called. |
| `check_result` | Check an earlier result | reads | no | Nothing. Reads the result of one earlier call of this connection. |

Results are bounded. A file read returns at most 500 lines, a command at most 64 KiB of each output stream, a screen outline at most 300 elements, and a picture is one JPEG. The text of a single result never exceeds 264 KiB, and each tool takes a limit (`length`, `max_results`, `max_entries`, `max_output_bytes`, `max_bytes`) so a caller can ask for less. An error is one sentence for the user and a stable code.

Three tools return an identifier the assistant passes back: `request_id` for `check_result`, `job_id` for a background job and `task_id` for an agent task. They are how the assistant follows up without running anything twice, not diagnostics.

The server also declares one UI resource, `ui://omega-relay/image-result-v2.html`. It shows the picture a capture tool already returned. It loads nothing from the network: its content security policy allows no connection, resource or frame origin.

## Sign-in

- OAuth 2.1 authorization code with PKCE (`S256`). Scopes: `omega:read`, `omega:execute`.
- Discovery: `https://omegarelay.dev/.well-known/oauth-protected-resource/mcp` and `https://omegarelay.dev/.well-known/oauth-authorization-server`. An unauthenticated call to `/mcp` answers `401` with a `WWW-Authenticate` header naming the first.
- Client identity: Client ID Metadata Documents from `claude.ai` and `chatgpt.com`, or dynamic client registration.
- Callbacks accepted: `https://claude.ai/api/mcp/auth_callback`, any `https://chatgpt.com/` callback, and a loopback `http://localhost` or `http://127.0.0.1` callback on any port for a client that registered it without one.
- Access tokens last 10 minutes. Refresh tokens rotate. A connection lasts 30 days, after which the user connects again.
- Transport: Streamable HTTP, stateless, `POST` only.

## Questions a reviewer may have

**This gives an assistant a terminal, file access, a browser and the screen. Why is that acceptable?**
The computer is the user's own, and nothing is reachable until the user has made the three choices above. The product is for people who want an assistant to do work on their machine, the same work they would do there themselves. Every changing tool is marked destructive so the assistant's confirmation applies, the user can pause or disconnect at any time, and Activity shows what was done.

**Does it get around the assistant's own sandbox or safeguards?**
No. It adds tools that run on a computer the user chose, under the user's operating-system account. It does not alter the assistant, its instructions or its permission prompts, and its skill tells the assistant to treat file contents, pages, the screen and command output as information, never as instructions.

**Is the Browser capability an unofficial connector to other services?**
No. It does not call any third party's API or use stored credentials. It operates the user's own Chrome through an extension, only in tabs the user shares one by one, the way the user would with a mouse and keyboard.

**`use_capability` runs operations that are not listed one by one. Is that a generic executor?**
It reaches only capabilities the owner added to their own computer, and only after the owner, or the user in the conversation, approved the exact set of operations. It is listed only for a connection whose owner did that. `list_capabilities` shows each operation and its input schema before any call. The review account has no added capability, so these three tools are not in its list.

**Can an assistant approve a capability by itself?**
`approve_capability` works only for a connection with Full access, only for the exact contract just listed, never against the owner's rejection, and the skill tells the assistant to ask the user first every time. The approval is recorded with the assistant's name.

**What happens to a call when the user pauses or disconnects?**
New calls are refused at once. A call already delivered to the computer may finish.

**Why does the tool list differ between connections?**
The server lists what that connection was granted and its computer can run. A connection with Files only sees the file tools, `list_computers` and `check_result`.
