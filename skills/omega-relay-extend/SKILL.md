---
name: omega-relay-extend
description: Help a person add their own capability to a computer connected through Omega Relay, and use, approve or retire it safely. Use when the built-in tools cannot do what they need, when list_capabilities shows something they added, or when a call reports that a capability is not approved or has changed.
---

# Extend Omega Relay with a capability of your own

A computer connected through Omega Relay has built-in capabilities: Files, Terminal, Browser, Screen & control and Agents. A user can add more. An added capability is a small program on their computer that offers named operations. Relay lets an assistant call one only after it has been reviewed and approved, and only while it is still exactly what was approved.

## First decide whether one is needed

Check the built-in tools first. Most requests are a file read, a command, or a browser step. Suggest a new capability only when the same narrow job will be done many times, needs a fixed and reviewable shape, or should be possible without granting Terminal.

## What you can and cannot do

You can design the capability with the user, list what a computer offers, call approved operations, and, with Full access, approve one after the user agrees.

Three tools serve this, and each is listed only when it can be used: `use_capability` once this connection can use an approved capability, `approve_capability` while this assistant has Full access on a computer where one is waiting for approval, and `list_capabilities` in either case.

You cannot install, update or remove a capability through Relay. Those are the owner's actions on their own computer and in Omega Relay. Do not tell the user that Relay installed something, and do not describe an installation procedure you cannot see. If they ask how, say that adding the program to the computer is their step and that Relay shows it once the computer reports it.

## Design rules Relay enforces

Relay sends an operation to the computer only if all of these hold. Use them as the design checklist.

1. The capability's name is its own. Names beginning `relay-` are reserved, and so is `ai-access`.
2. No operation is called `unregister`.
3. Every operation has a closed input schema: an object, `additionalProperties: false`, every nested object closed too, sizes and ranges bounded. Input schemas may not use `pattern` or `$ref`.
4. Every operation declares a closed, bounded result schema in `x-relay-output-schema`. Relay drops any result that does not match it.
5. Every operation is protected in one of two ways:
   - **Ticketed.** It declares `x-relay-execution-contract: "relay-user@1.0.0"`, Relay's `x-relay-execution-key`, and a `_relay` field. Relay then sends a signed ticket with each call. The program must verify the signature, refuse the ticket after it expires (60 seconds), match it to the request id, operation and arguments, record the request before acting, never act twice on one request, and answer return code 8 if it was interrupted. Use this for anything that changes something.
   - **Safe to repeat.** It reports `idempotent: true` and gets no ticket. Use this only for operations that are harmless if they run late or more than once, because that can happen.
6. Anything else is refused.

Tell the user plainly: Relay checks the declared shape, not the program's code. A capability that claims to check tickets and does not, or calls something safe to repeat when it is not, is unsafe in a way Relay cannot detect.

## Review and approval

1. Call `list_capabilities`. For each capability it shows the `status` (`approved`, `needs approval` or `rejected by the owner`), the operations with their input schemas, whether each is safe to repeat, and any operations Relay will not send (`unavailable`).
2. For a capability that `needs approval`, show the user its name, every operation and what each does to their computer. Say which operations change things.
3. **Ask the user before the first approval of a newly created capability, every time, in this conversation.** Approve only what they agreed to, and only the listing you just showed them.
4. If this assistant has Full access on that computer, call `approve_capability` with the capability name and the `contract` value from that listing. Otherwise the user approves it in Omega Relay.
5. If the owner rejected a capability, you cannot approve it. Do not ask them to reconnect to get around that.

Approval is tied to that exact listing. Any change, including a new version, a new operation or a changed schema, makes the capability need approval again. Show the user what changed before asking again.

## Use it

Call `use_capability` with `capability`, `operation` and `arguments` that fit the listed input schema. Results follow the same rules as every other tool: a finished call returns the computer's result, `pending` and `unknown` are checked with `check_result` and never sent again, and an error is one sentence with a code. See [troubleshooting](../omega-relay/references/troubleshooting.md).

With chosen capabilities instead of Full access, a connection reaches only the operations it was given.

## Retire it

Only the owner can remove a capability, in Omega Relay. Relay's own capabilities cannot be removed at all. If a capability should stop being used, tell the user to remove or reject it there.
