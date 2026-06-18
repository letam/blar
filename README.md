# blar — 1000 words, ship-a-day

One textarea, a word counter that encourages but never blocks, and a **publish** button. Publish commits your post straight to a GitHub repo as a date-named markdown file. The repo's `posts/` folder *is* the public page (GitHub renders markdown for free).

The whole point is the one act you avoid: hitting **publish**, not drafting.

- **No save, no autosave, no draft list — on purpose.** Close the tab with unpublished words and they're gone (a `beforeunload` warning says so). The only way to make words survive is to make them public.
- **Publish is always enabled.** The counter says `847 words — ship it anyway`. There is no word floor — `shipped =` is three *readable* posts, not three *1000-word* posts.
- **No backend you run.** GitHub is the backend. Your token lives only in this device's `localStorage` (`blar` → `{token}`). The one network call is a `PUT` to the GitHub contents API.

## one-time setup (do this first — it's the only real risk)
1. **Make the posts repo** (public, so the page is readable):
   ```
   gh repo create neoselfy/blar-posts --public --clone
   cd blar-posts && mkdir posts && touch posts/.gitkeep
   git add -A && git commit -m "init posts" && git push
   ```
2. **Make a fine-grained token:** github → Settings → Developer settings → Fine-grained tokens → only the `blar-posts` repo → Repository permissions → **Contents: Read and write**. Copy it.
3. **Sanity-check the path works before building anything else:**
   ```
   curl -X PUT -H "Authorization: Bearer YOUR_TOKEN" \
     -H "Accept: application/vnd.github+json" \
     https://api.github.com/repos/neoselfy/blar-posts/contents/posts/test.md \
     -d '{"message":"test","content":"'"$(printf 'hello' | base64)"'"}'
   ```
   If that creates `posts/test.md`, blar will work. Delete the test file.
4. In `index.html`, set `REPO` (top of the `<script>`) to your `owner/repo`. On first load, blar asks for the token once and stores it.

## what's intentionally NOT here (v0 cuts)
the 1000-word gate · a custom/styled public page · streak / "went dark for N days" · login · a Worker/serverless layer · draft saving · post editing/deletion · frontmatter/tags · RSS · subscribe · comments · spellcheck · preview. All v1, after 14 posts exist.

> Same-day second post won't clash: if `posts/YYYY-MM-DD.md` already exists, blar retries once with a time-stamped filename — still a create, no SHA fetch.

## deploy the app
```
gh repo create neoselfy/blar --public --clone   # the app (separate from blar-posts, the content)
# copy index.html in
git add -A && git commit -m "blar v0" && git push
# github → Settings → Pages → main / root → enable  →  https://neoselfy.github.io/blar
```

## build plan reminder
De-risk the `curl` first. Then ship **post #1 about why you're building blar** before any styling exists. Posts #1–3 go live before you touch CSS. Sunday styling is capped at one hour; if it runs out, it ships unstyled — the posts are already live.

## shipped =
**3 of your own posts**, real and unedited, publicly readable at `github.com/neoselfy/blar-posts/tree/main/posts`, each committed via the publish button.
