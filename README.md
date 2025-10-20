# Automate Remote Script Execution with n8n  
*(Schedule and run any command or script on a remote server via SSH)*

This guide explains how to use **n8n** to connect to any Linux server, run your desired script or command on a schedule, and optionally process the output for further automation.

The workflow is **generic** — it can be used for any automation tasks such as backups, monitoring, cleanup, service restarts, etc.

---
## ✅ Workflow Summary

| Component                   | Description                           |
| --------------------------- | ------------------------------------- |
| `Remote Script Runner.json` | The n8n workflow file                 |
| `remote-server-ssh`         | SSH credential for the remote server  |
| Scheduler                   | Triggers workflow automatically       |
| Executer                    | Executes command via SSH              |
| Exporter                    | Parses and outputs JSON or plain text |

---

## Use Cases

* Automating cross-server backups
* Cleaning temporary or cached files
* Automatically restarting services or containers
* Running monitoring or health-check scripts
* A visual, easy-to-manage alternative to cronjobs
