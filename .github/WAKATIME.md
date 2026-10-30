# WakaTime README updater

The `Waka Readme` workflow refreshes the profile's all-time language graph daily
at 00:00 UTC, or when run manually. It needs a WakaTime personal OAuth token.
Plugin API keys are write-only and cannot be used to read coding statistics.

## Credential setup

Complete this before merging the OAuth migration:

1. Open [WakaTime's personal OAuth token page](https://wakatime.com/oauth).
   Authorize read access to coding statistics (`read_stats`). The updater reads
   `/api/v1/users/current/stats/all_time`; it does not need write, email, or
   organization access. Check the permissions shown before authorizing.
2. Save the token directly as the repository Actions secret
   `WAKATIME_OAUTH_TOKEN` in
   [this repository's secrets settings](https://github.com/waltershewmake/waltershewmake/settings/secrets/actions).
   Do not put it in a commit, issue, pull request, chat, or log.
3. After the workflow change is merged, manually run `Waka Readme` on `main`.
   This run can commit an updated graph to `README.md`. Confirm it succeeds and
   the graph still shows the all-time date range, total, and language breakdown.
   A successful run may make no commit if the statistics have not changed.

The action's input is still named `WAKATIME_API_KEY`, but its value comes only
from `secrets.WAKATIME_OAUTH_TOKEN`. There is no fallback to the old plugin key.
A missing token fails immediately with a setup message. Leave the editor's
plugin API key unchanged.

The workflow pins the upstream action to its OAuth-documented revision and
grants its GitHub token only `contents: write`, which it uses to update the
README. No separate GitHub personal access token is needed.

If WakaTime returns 401 or 403, check that the OAuth grant is active and includes
stats access, then replace the Actions secret if necessary. Use the personal
token flow above; the generic implicit OAuth flow (`response_type=token`)
expires after 12 hours and is unsuitable for this daily job.

## References

- [WakaTime authentication and scopes](https://wakatime.com/developers#authentication)
- [WakaTime stats API](https://wakatime.com/developers#stats)
- [Upstream action's OAuth migration](https://github.com/athul/waka-readme/pull/181)
