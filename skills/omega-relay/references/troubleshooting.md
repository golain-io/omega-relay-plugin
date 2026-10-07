# Troubleshooting

Every error is one sentence for the user and a `code`. Do not work around an error with another connector or a local tool.

| Code | What it means | What the user can do |
| --- | --- | --- |
| `NO_COMPUTER` | This connection has no computer. | Connect a computer in Omega Relay, then reconnect this assistant. |
| `COMPUTER_REQUIRED`, `COMPUTER_NOT_FOUND` | Several computers, or a name that is not one of them. | Pass a name from `list_computers`. |
| `ACCESS_PAUSED` | The owner paused access. | Resume it in Omega Relay. |
| `GRANT_REQUIRED` | This connection was not given that capability on that computer. | Give this assistant that capability, or Full access, in Omega Relay. |
| `SCOPE_REQUIRED` | The connection was approved for reading only. | Reconnect and allow changes. |
| `TOOL_NOT_ON_DEVICE` | The capability is not installed there yet. | Wait for setup to finish in the Omega Relay app. |
| `DEVICE_OFFLINE` | The computer is not connected. | Open the Omega Relay app on it. |
| `DEVICE_CAPABILITY_NOT_READY`, `EXECUTION_SAFETY_NOT_READY`, `GRANT_SCHEMA_DRIFT`, `RESULT_SCHEMA_REQUIRED` | The capability on that computer is not running properly or needs an update. | Update the Omega Relay app. |
| `RECONSENT_REQUIRED`, `GRANT_CONTRACT_MISMATCH` | The capability changed after a chosen approval. | Approve this assistant again in Omega Relay. |
| `NEEDS_YOU` | This capability needs the user to do one thing on the computer first. `next` names it and the sentence says it. | Do that step on the computer. |
| `TURNED_OFF`, `NOT_AVAILABLE` | This capability is switched off or cannot be used on that computer. | Turn it on in the Omega Relay app on that computer. |
| `NO_ANSWER`, `ASK_AGAIN` | The computer did not answer a read in time, or had already answered it once and kept nothing of it. Nothing changed. | Nothing. Call the tool again. |
| `BROWSER_SESSION_CHANGED`, `BROWSER_SESSION_CHANGED_OR_EXPIRED`, `TAB_NOT_SHARED` | Chrome's connection or the tab changed after it was listed. The call was not carried out. | Nothing. Call `browser_tabs` and use the new ids. |
| `STALE_SNAPSHOT` | The page changed since the snapshot. | Nothing. Take a new snapshot. |
| `SHARE_A_TAB_IN_CHROME`, `SHARE_A_TAB_FIRST` | No tab is shared with this assistant yet. | Share a tab in Chrome on that computer. Then call `browser_tabs`. |
| `SCREEN_CHANGED` | The windows or the screen share changed after they were listed. The call was not carried out. | Nothing. List the windows again, or start the share again. |
| `OWNER_LIVE_VIEW` | On that computer only its owner starts or stops a live view of the screen. Looking and acting need nothing started. | Nothing. Use `screen_snapshot`, then act. |
| `OWNER_SHARE`, `OWNER_SESSION` | The owner started this screen share or live view. | Only the owner can end it. |
| `INVALID_SCREENSHOT_RESULT` | The picture the computer sent could not be used. | Nothing. Take the picture again. |
| `JOB_LIMIT` | The computer already keeps as many background jobs as it can. | Nothing. Clear finished ones with `cancel_job`. |
| `KEY_NOT_ALLOWED` | That key cannot be pressed in a tab. | Nothing. Use a letter, a digit or a named key such as Enter. |
| `BROWSER_CONNECTION_LIMIT` | Too many assistants are connected to Chrome on that computer. | Disconnect one in Chrome. |
| `AUTHORIZATION_EXPIRED` | The call reached the computer too late and was not carried out. | Nothing. Call the tool again. |
| `DEVICE_REFUSED` | The computer itself said no. A reason it labels, such as a folder outside what it allows, is passed on as written. Otherwise `detail` holds its own wording. | Follow the sentence in the Omega Relay app. |
| `INVALID_TOOL_ARGUMENTS`, `TOOL_ARGUMENT_LIMIT` | The arguments do not fit the tool. | Nothing. Correct the call. |
| `RATE_LIMITED` | Too many calls in one minute. | Nothing. Wait, then continue. |
| `MCP_OUTPUT_LIMIT` | The result was too large. | Nothing. Ask for less: fewer lines, a smaller page. |
| `OUTCOME_UNKNOWN` | The action may have run. | Nothing yet. Use `check_result`. Do not repeat it. |
| `INVOCATION_NOT_ISSUED`, `INVALID_REQUEST_ID` | That is not a request id, or not one of this connection's calls. | Nothing. Use the id a call returned. |
| `EXECUTION_SIGNER_NOT_READY` | Relay could not authorize the call. | Try later. If it persists, contact support. |

For capabilities the user added:

| Code | What it means | What the user can do |
| --- | --- | --- |
| `CAPABILITY_NOT_FOUND` | That computer has no capability with that name. | Nothing. Call `list_capabilities` for the names. |
| `CAPABILITY_NOT_APPROVED` | Nobody has approved it yet. | Approve it in Omega Relay, or agree in chat if this assistant has Full access. |
| `CAPABILITY_CHANGED` | It changed since it was reviewed. | Review and approve it again. |
| `CAPABILITY_REJECTED` | The owner rejected it. | Only the owner can change that, in Omega Relay. |
| `FULL_ACCESS_REQUIRED` | This assistant may not approve capabilities. | Approve it in Omega Relay. |
| `NOT_USER_DEFINED` | That is a built-in capability. | Nothing. Use its own tools. |

A tool that was listed earlier and is gone now means access, installation or the computer's own settings changed. Call `list_computers` again.

A result from `check_result` is the computer's own answer to that one call. Ids in it are not the ids the tools take: list the tabs or windows again to get those.
