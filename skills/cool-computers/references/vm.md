# VMs

Use a VM only when the work needs a process that keeps running, a schedule, a long task or an Ubuntu package or tool. A page, an app that runs in the browser, a small API, a database and an email address all work as artifacts (`publish.md`).

## What gives a VM

- `cool_create_computer` (`name`, up to 63 lowercase letters, digits and single hyphens; `visibility`; `auto_delete_after` such as `2h`, or left out for never) makes a new computer with a VM. It serves nothing until a program runs. For a page or a site, use `cool_publish` instead, with `visibility: "private"` for a private one.
- `cool_give_vm` (`computer`) or `cool_run_service` on a site, or a publish whose `cool.json` has `run`, gives that site an always-on VM; the result says so. Only the owner can do this. The account's first VM starts its 72-hour trial, with no card; after it, VMs need a subscription.
- Nothing else gives a VM: `cool_exec` and `cool_expose_port` on a site without a VM are refused (`needs_vm`), and the file tools never do.
- When no host has room for a new VM, the call is retried on every host for up to 30 seconds, then fails with nothing changed: try again in a minute.

What changes:

- A site's published files move to `/home/runtime/app` and keep serving at the same URL until a program's port is exposed; functions, secrets and the database keep working. `cool.json`, hidden files such as `.env` and `node_modules/` are never served. Every other file in `/home/runtime/app` can be fetched on a public site, so keep code and data that must not be public outside it.
- Once a service exposes a port, the program answers every path, `/` included, except `/api` on a site with functions, and `/home/runtime/app` is never served as files again, even after the port is removed: the program serves its own static files.
- Programs get `COOL_COMPUTER` (the computer's name), `COOL_URL` and `COOL_EMAIL`; files they create are readable by the site (umask 022).
- It is one-way: `cool_publish` to the site is refused from then on. `cool_write_files` writes its files (anywhere under `/home/runtime`; a relative path starts at `/home/runtime/app`), `cool_mkdir` creates a folder, and `cool_list_files` and `cool_read_file` read them. `cool_exec` (`computer`, `command`, optional `cwd` and `timeout` in seconds, 60 by default, at most 300) runs one shell line and returns its exit code and the end of its output.
- The computer stays on until it is deleted (`cool_delete_computer`, only when the user asked).

## The computer

- 1 vCPU, Ubuntu 24.04, no swap. User `runtime` with passwordless `sudo`. Preinstalled: Python 3, Node 22, Go, git, gh, uv, build tools, Claude Code, Codex and Pi.
- Memory: one VM can use up to the account's full 8 GiB (`free` shows 8 GiB), but all the account's computers share that one 8 GiB pool, and the account has at most 20 VMs. Memory a computer frees goes back to the pool, so an idle computer holds about 110 MiB. When the pool has too little room, giving another site a VM is refused at once (`account_memory_full`); stopping a program that holds memory, or deleting a computer, makes room.
- Outbound internet is open by default; private network ranges are blocked. `cool_get_computer_network`, `cool_set_computer_network` and `cool_list_network_denials` show and change it.

## Services

- `cool_run_service` saves and starts the computer's one program and keeps it running:
  - `command`: one shell line that starts the long-running program, such as `node server.js`. It runs again on every restart, so install dependencies once first with `cool_exec`; without that, `npm install && node server.js` works but reinstalls each time.
  - `port`: the URL reaches the program on that port, for every path but a site's functions (`/api`); it is ready when the port accepts connections. Ports 3129, 18080 and 18088 are reserved; 8080 is free. Leave `port` out for a bot or worker: it is running once it has stayed up for 5 seconds, and the URL is left as it was. Never add a dummy port.
  - `cwd`: defaults to `/home/runtime/app` with a port and `/home/runtime` without one.
  - `secrets`: a secret set whose keys the program gets as environment variables at every start.
  - `env`: more environment variables, as `{"KEY": "value"}`; keep keys in a secret set instead.
- A crashed program is restarted automatically after 1 second, then longer after each crash, up to a minute; one that exits with code 0 stays exited. A program still installing or starting returns `status: starting` and keeps starting in the background: check it with `cool_get_service`, and do not run it again.
- `cool_get_service` shows its status (starting, running, restarting, stopped, exited or failed), readiness, restart count, last exit and last output; `cool_get_service_logs` its output. `cool_restart_service`, `cool_stop_service` (keeps its configuration) and `cool_clear_service` (removes it) change it. Calling `cool_run_service` again replaces it. After `cool_write_files` changes the program's files, restart it.
- One service per computer. Another long-running program needs a systemd unit started with a command (`sudo systemd-run --unit=worker --uid=runtime --working-directory=/home/runtime/app --property=Restart=always node worker.js`), run with `cool_exec`.

## Secrets and integrations

- `cool_set_secrets` creates or replaces a whole secret set (`secret_set`, `values` as `{"KEY": "value"}`, optional `proxy` as `{"KEY": "<host>"}` to keep a key off the computer). Never store your Cool Computers key in one; a value shaped like one is refused. `cool_list_secrets`, `cool_get_secret_metadata` (`secret_set`) and `cool_list_secret_audit` show names, keys and history, never values.
- Inside the computer, `cool secrets exec <set> -- <command>` gives one process the keys.
- Integrations: Claude, Codex, Grok and GitHub (`cool_list_integrations`, `cool_connect_integration`; Claude returns a CLI command to finish connecting). Pi uses the Claude and Codex connections. Credentials stay on Cool Computers' servers; inside a computer, git and gh use GitHub automatically.
- A shared computer gets no secret sets or integrations, except the set its own `cool.json` names for its functions.

## GitHub workspaces

Call `cool_plan_switch_workspace` before `cool_switch_workspace`, and apply only a ready plan that matches the requested repository and branch.
