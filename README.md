# Roleforge — one book per file, one Git commit per book

Every role book has its own file in `books/`, e.g. `books/keepers.json` and `books/between.json`. The role distributor reads all books in that directory, then combines any selected books for Chaos Mode. Each time an editor chooses **Publish (one commit per book)**, Roleforge uses GitHub's Contents API to create **a separate commit for each book that actually changed**. Editing only one book creates one commit. Editing three books creates three successive commits. Deleting a book creates a commit deleting just its file. Secret assignments, player names, and quotas are never published.

## First-time GitHub setup

1. Create a PUBLIC GitHub repository (or use your existing Roleforge repository). Place `index.html` in the repository root and upload the three files in `books/` while keeping their paths.
2. If you want the **initial** files to have separate commits too, upload `index.html` and commit, then upload **each `books/*.json` one at a time**, committing each individually. (Uploading all files in one batch gives one initial commit.) You can also use Git locally and run `git add books/keepers.json && git commit -m "Add Keepers & Reapers"` followed by analogous commits for the other files.
3. Enable GitHub Pages in repository Settings → Pages (branch `main`, root `/`), or continue hosting `index.html` on Netlify.
4. Open Roleforge → Shared GitHub books, choose your repository, branch and **Books folder `books`**, then Connect. Share the hosted URL with other players.
5. To publish edits from Roleforge, make a fine-grained personal access token at https://github.com/settings/personal-access-tokens/new restricted to the repository with **Contents: Read and write**. Enter it under editor settings and choose Publish. The token stays in the current page session only, not in downloads, exports or localStorage. Do not use a token on a shared/untrusted device.

## Existing repository using `books.json`

No need to delete or manually rewrite the old `books.json`. Upload the new `index.html`, then open it and connect. If there is no `books/` directory yet, Roleforge can load the legacy `books.json`, and a single **Publish** operation creates an independent file/commit for each book. It leaves the old combined `books.json` untouched (you may archive it later). Alternatively, upload the included `books/*.json` files directly and start using the new folder immediately.

## Book file shape

```json
{
  "schemaVersion": 2,
  "id": "keepers",
  "name": "Keepers & Reapers",
  "categories": [
    {
      "name": "Villager",
      "color": "#e9b769",
      "roles": [
        {"name": "Bookkeeper", "copies": 1, "description": ""}
      ]
    }
  ]
}
```

Each book's `id` is used as its filename: `books/<id>.json`. Keep IDs unchanged when renaming a book. The app scans the folder, so no manifest needs updating when books are added or removed. If two editors change **different** books, Roleforge can publish both without overwriting the other book; if they change the **same** book, the outdated editor is warned. A change that affects several books (such as renaming a shared category) will create one commit per affected book. GitHub APIs do not make these multi-commit publishes atomic: if a request fails halfway, previously completed commits remain, and the app preserves unpublished edits for retry.

The repository should be public because readers fetch the books without credentials. Neither role assignments nor editor tokens should appear in public files. Browser clients can refresh manually or automatically after about 5 minutes; it is not live instant sync.
