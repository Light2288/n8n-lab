# Workflow examples

[n8n-workflows](https://github.com/Light2288/n8n-workflows/tree/chore/workflow-repositories) is the authoritative home for reviewed workflow JSON, example documentation, and the manual export/review/import procedure. The link targets its implementation branch until a separate merge into main.

n8n-lab owns the runtime and operations. Keep workflow exports in the companion repository. Editor saves update the running database; Git changes require an explicit JSON import and local credential configuration.

Workflow definitions are separate from a full instance backup. Accounts, credential secrets, settings, and execution history require the lab's [backup and recovery procedure](../docs/backup-and-restore.md).
