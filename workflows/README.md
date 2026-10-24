# Workflow exports

Store reviewed workflow JSON here, using descriptive names such as `github-release-digest.json`. This directory starts empty apart from these instructions: no USDZ migration or provider credentials are included.

## Export, review, commit

1. Save the workflow in the local n8n editor.
2. Use the workflow menu's **Download** action to export JSON. Menu wording can vary by n8n release.
3. Review the JSON before moving it into this directory. Remove private `pinData`, personal/customer samples, tokens, API keys, inline passwords, authorization headers, private URLs, and sensitive values in Code or HTTP Request nodes. Unpinning editor data does not remove other embedded values.
4. Review credential references: exports can include credential IDs and names even when secret credential values are omitted. Remove or sanitize sensitive identifiers and names. Importers will need to select their own credentials.
5. Use Git to inspect exactly what will be published:

   ```sh
   git diff -- workflows/
   git add -- workflows/your-workflow.json
   git diff --cached -- workflows/your-workflow.json
   git commit -m "feat: add reviewed example workflow"
   ```

For a new untracked file, inspect its complete JSON before staging; the staged diff then shows its full contents. Review every export again after subsequent edits. The ignore rules are a guardrail, not a secret detector. Do not force-add ignored credential exports or private samples.

Use `workflows/private/` or `workflows/scratch/` only for ignored local experiments. Reviewed workflows should use mock/public data and portable configuration.

## Import explicitly

Use **Import from File** in the editor to select an exported JSON file, recreate or select local credentials, review triggers and destinations, then save it. Verify the imported workflow before enabling schedules or publishing it.

**Editor saves update SQLite; Git commits update JSON files. Neither operation updates the other.** Pulling a changed JSON file does not update the running instance. Export again after editor changes, and import explicitly when adopting repository changes.

JSON exports are portable workflow definitions. They are not a full instance backup: accounts, credentials, settings, execution history, and other database state require the separate [backup and recovery procedure](../docs/backup-and-restore.md).
