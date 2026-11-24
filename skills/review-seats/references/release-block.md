# Release block

Follow this reference when a repo's work ends in a release. The release gets its own block, separate from the
branch's last task block and separate from the merge block.

## 1. When it runs

The release block runs after the release branch's PR is merged and never before. The merge has its own block, and
the user says "done" after it. The merge block is PowerShell and holds these steps in order. The PR number, repo and
branch below are made up, and the real ones go in when the block is given.

```powershell
gh pr merge 12 -R owner/repo --merge --delete-branch
git switch main
git pull
git branch -d feature/csv-export
```

## 2. Inputs the agent reads itself

The agent reads these after the merge, with read-only commands:

- The merge commit's full 40-character hash, from `gh pr view <number> --json mergeCommit`.
- A check that local `main`, `origin/main` and that hash are the same commit. When they differ, the agent stops and
  tells the user, because the tag would land on the wrong commit.

The agent gives the block only once the hash exists, so the block never holds a placeholder.

Before it gives the block, the agent also lists each release asset's contents and metadata, for example the entries
of an archive or the tags of a media file. It checks them against the ship list and the project's term list and
never quotes a term.

## 3. The release notes file

The notes are the CHANGELOG's section for the version. The agent writes them to a gitignored file, for example
`INTERNAL/release/notes-v1.2.0.md`, with each paragraph and each bullet on one line.

GitHub release bodies turn every newline into a hard break. A `.md` file in a repo does not. A CHANGELOG section
wrapped at 100 columns reads fine in the repo and breaks sentences mid-line on the release page.

Before it gives the block, the agent previews the notes rendered with hard breaks on (for example markdown-it with
`breaks: true`) and reads the result. It also checks that the CHANGELOG date for the version matches the release day.

## 4. The repo's About box

The About box holds a description, topics and a website. The agent shows the wording to the user for OK before it
writes the block. When the website stays empty, the agent says why. When the About box is already right, the block
leaves out the `gh repo edit` line.

## 5. The block

The block is PowerShell and holds these steps in order. The hash and repo below are made up.

```powershell
git tag -a v1.2.0 0123456789abcdef0123456789abcdef01234567 -m 'Release 1.2.0'
git push origin v1.2.0
gh release create v1.2.0 -R owner/repo --verify-tag --title 'v1.2.0' --notes-file INTERNAL/release/notes-v1.2.0.md
gh repo edit owner/repo --description 'One plain sentence about what the project does.' --add-topic topic-one,topic-two
```

Release assets go after the tag in `gh release create`. Text in single quotes follows the quoting rule in SKILL.md
section 2, which also says how to write an apostrophe.

## 6. After the user runs it

Once the user says "done", the agent checks the live result and reports what it found:

- The release page shows the notes with no mid-sentence breaks.
- Each asset downloads, for example HTTP 200 on each asset URL.
- The tag exists on the remote.
- The About box shows the description and topics.
