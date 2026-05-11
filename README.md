# Starshelf

A self-rebuilding index of GitHub stars, sorted by the user-lists they belong to. Use this template, point it at your own account and you get the same thing for your stars.

Every morning a GitHub Action queries the GraphQL API, pulls the [lists you keep on your stars page](https://docs.github.com/en/get-started/exploring-projects-on-github/saving-repositories-with-stars#organizing-starred-repositories-with-lists), then rewrites the catalog section of the README. No databases, no third-party services, no maintenance after the first setup.

## Why

The default GitHub stars tab works fine for a few dozen entries but becomes unusable past a few hundred. User-lists fix the sorting problem but the UI is buried two clicks deep and offers no overview. Rendering everything as a Markdown table per category gives you a navigable bookmark file that lives in `git`.

## Use it for your own stars

1. Click **Use this template** above to create a new repo from this one (or fork the repo if you prefer).
2. Sort your stars into [lists on GitHub](https://docs.github.com/en/get-started/exploring-projects-on-github/saving-repositories-with-stars#organizing-starred-repositories-with-lists). The script reads whatever lists you have and renders one collapsible section per list.
3. Create a [classic personal access token](https://github.com/settings/tokens/new) with the `read:user` scope.
4. Add the token as a repository secret named `STARS_TOKEN` (`Settings` → `Secrets and variables` → `Actions`).
5. Trigger the **Build stars** workflow once from the Actions tab. After that it runs daily at 06:00 UTC.
6. Replace this README in your new repo with whatever you want; just keep the `<!-- STARS:START -->` and `<!-- STARS:END -->` markers somewhere so the script knows where to write.

The script identifies you by querying `viewer.login`, so no code change is needed after the setup.

## What you get

A `## ⭐ Curated Stars` block in the README, with one collapsible `<details>` section per list. Each row shows the repo name, star count, a freshness badge and the description.

| Badge | Meaning |
| --- | --- |
| 🔥 | Pushed within the last 7 days |
| 💤 | No push in 365+ days |
| 📦 | Archived |

Categories sort by item count then by total stars. Repos within a category sort by freshness then by stars.

## Mirror it on your profile

If you want the same catalog rendered on your `username/username` profile README, paste an empty marker block where you want it to appear:

```markdown
<!-- STARS:START -->
<!-- STARS:END -->
```

Then add a workflow that fetches the rendered block from your stars repo and pastes it in. A minimal example lives in [`examples/sync-to-profile.yaml`](examples/sync-to-profile.yaml). Edit the `STARS_SOURCE` env var at the top of the workflow to point at your stars repo.

## How it works

`generate_stars.py` is around 200 lines of stdlib-only Python. It does three things:

- Queries `viewer.lists` and pages through every repo in every list, retrying on 502/504.
- Picks each repo's first list as its category.
- Rewrites the section between `<!-- STARS:START -->` and `<!-- STARS:END -->` in the README. If the markers appear more than once (for example inside the docs above), the script always rewrites the last pair.

The workflow uses a `concurrency` group so two runs cannot fight over the README.

## License

MIT. Use it, change it, do whatever you want.

---

<!-- STARS:START -->
*The script populates this section on first run.*
<!-- STARS:END -->
