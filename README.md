# Bulk Launcher landers

Create a repository from this template, then connect it in Bulk Launcher Settings → Connect GitHub. Install the GitHub App using **Only select repositories** and choose this repository.

## Connect your AI

Connect your AI to Bulk Launcher's MCP server from the app settings. Ask it to improve or check the page using the tools available on that connection. Every edit is a draft: show the preview and get the user's explicit yes before publishing.

Pages belong in landers/<folder>/index.html with metadata in lander.json. Read AGENTS.md before editing. Images stay hosted by URL, not in git.

## Action setup

Set the repository variable BULK_LAUNCHER_URL to your app's HTTPS origin. Add the secret BULK_LAUNCHER_TOKEN using an app-created PAT scoped to landers:read (and landers:publish only for the optional publish action).

Phase 1 provides connection and managed files. Checks/previews become available in Phase 2, initial export and ongoing sync in Phase 3, and the manual publish action in Phase 4. Until then a 404/501 check response is explicitly skipped, not a successful content check. Add the stable **Bulk Launcher** check as required branch protection once checks are available. Nothing in Phase 1 publishes a page.

## Publish from an Action or terminal

Use an owner/admin's workspace PAT with `landers:publish`. Run **Lander checks → Run workflow** with a folder and optional full commit SHA. The commit must have a mainline version in the connected workspace.

```sh
curl --fail-with-body --silent --show-error \
  -H "Authorization: Bearer $BULK_LAUNCHER_TOKEN" \
  -H 'Content-Type: application/json' \
  --data '{"folder":"landers/example","commit":"<40-character commit SHA>"}' \
  "$BULK_LAUNCHER_URL/api/github/v1/publish"
```

Omit `commit` for the latest mainline version; alternatively send `lander_id` instead of `folder`, and `version_id` instead of `commit`. A failed gate returns 422, the manual-publish lock 423, missing scope or a non-admin PAT 403, and the 30-per-grant/hour limit 429. Review and approve active content in the app, never with a token. The publish audit records the version, commit and grant. No token belongs in the repository.

Publish on merge is optional and evaluates **only Bulk Launcher checks**, not GitHub branch protection or other required checks. Direct pushes to the default branch also qualify. Enable GitHub branch protection to require your other checks before merging. PAT-backed publish/rollback is owner/admin only through MCP as well as Actions and CLI.
