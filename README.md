# GitHub CLI (gh) for day-to-day work

`gh` is GitHub's official command line tool. It handles the GitHub side of your work, like pull requests, reviews, issues and CI runs, from the terminal, so you spend less time clicking around github.com.



## gh or git?

They work side by side, and the split is simple:

* **git** handles everything in your local repo: branches, commits, merges, pushes.
* **gh** handles everything that only exists on GitHub: pull requests, reviews, issues, releases, Actions runs.

A typical day uses both: `git` to commit and push a branch, then `gh` to open the pull request and follow it through review.

The rest of this guide covers one command group per section, each with a cheat sheet you can come back to. In the commands, `<n>` means a PR or issue number and `<branch>` means a branch name.



## Install and get help

| Platform         | Install command                  |
| ---------------- | -------------------------------- |
| macOS (Homebrew) | `brew install gh`                |
| Windows (winget) | `winget install --id GitHub.cli` |
| Debian / Ubuntu  | `sudo apt install gh`            |
| Fedora           | `sudo dnf install gh`            |

Distro packages can lag behind; for the latest version, and for other platforms, follow the instructions on <a href="https://cli.github.com/" target="_blank">cli.github.com</a>. Update later with the same package manager, e.g. `brew upgrade gh`.

Every command has built-in help that ends with real examples. It is often quicker than searching the web:

```bash
gh --help              # every command group
gh pr --help           # every pr subcommand
gh pr create --help    # every flag for one command
```



## gh auth: signing in

Run `gh auth login` once per machine. It asks a few questions (GitHub.com or Enterprise, HTTPS or SSH) and signs you in through the browser.

| I want to…                                       | Command                          |
| ------------------------------------------------ | -------------------------------- |
| Sign in                                          | `gh auth login`                  |
| See who I'm signed in as, and the token's scopes | `gh auth status`                 |
| Switch between signed-in accounts                | `gh auth switch`                 |
| Add a permission scope (e.g. for deleting repos) | `gh auth refresh -s delete_repo` |
| Let plain `git` use gh's login for HTTPS         | `gh auth setup-git`              |
| Print the current token for a script             | `gh auth token`                  |
| Sign out                                         | `gh auth logout`                 |

Treat the output of `gh auth token` like a password: never paste it into chats, issues or commits.



## gh repo: repositories

Commands run inside a cloned repo act on that repo. Outside one, pass `OWNER/REPO`, e.g. `gh repo view cli/cli`.

| I want to…                                   | Command                                                        |
| -------------------------------------------- | -------------------------------------------------------------- |
| Clone a repo without copying its URL         | `gh repo clone OWNER/REPO`                                     |
| Create a new repo from the current folder    | `gh repo create my-app --private --source=. --push`            |
| Fork a repo and clone my fork                | `gh repo fork OWNER/REPO --clone`                              |
| Read a repo's description and README         | `gh repo view OWNER/REPO`                                      |
| Open the repo in the browser                 | `gh repo view --web`                                           |
| List repos for a user or org                 | `gh repo list OWNER --limit 50`                                |
| Update my fork from the original             | `gh repo sync`                                                 |
| Pick which remote gh uses for PRs and issues | `gh repo set-default`                                          |
| Rename, archive or delete                    | `gh repo rename new-name`, `gh repo archive`, `gh repo delete` |

`gh repo set-default` matters when a repo has several remotes, such as a fork plus the original (`upstream`). Without it, gh may open PRs against the wrong one. `gh repo delete` cannot be undone, and it needs the `delete_repo` scope from `gh auth refresh`.



## gh pr: pull requests

This is the group you will use most. Commands that act on one PR accept its number, branch name or URL. Leave it out and they use the PR for the branch you are on.

#### A typical branch-to-merge run

```bash
git switch -c fix-login-redirect        # 1. branch
git commit -am "Fix login redirect"     # 2. commit
git push -u origin fix-login-redirect   # 3. push
gh pr create --fill                     # 4. open the PR (title and body from your commits)
gh pr checks --watch                    # 5. wait for CI
gh pr merge --squash --delete-branch    # 6. merge once approved
```

After step 4, more commits need no `gh` command: `git push` again and the PR updates itself.

#### Create

| I want to…                                   | Command                                               |
| -------------------------------------------- | ----------------------------------------------------- |
| Open a PR, answering prompts                 | `gh pr create`                                        |
| Use my commit messages as title and body     | `gh pr create --fill`                                 |
| Spell everything out, no prompts             | `gh pr create --base main --title "..." --body "..."` |
| Read the description from a file             | `gh pr create --body-file pr.md`                      |
| Open a draft                                 | `gh pr create --draft`                                |
| Open a PR for a branch I haven't checked out | `gh pr create --head <branch>`                        |
| Ask for reviewers and labels up front        | `gh pr create --reviewer user1,user2 --label bug`     |
| Finish it in the browser                     | `gh pr create --web`                                  |

#### Find and read

| I want to…                                       | Command                                        |
| ------------------------------------------------ | ---------------------------------------------- |
| My PRs, PRs awaiting my review, this branch's PR | `gh pr status`                                 |
| List open PRs                                    | `gh pr list`                                   |
| List only mine                                   | `gh pr list --author "@me"`                    |
| Check if a branch already has a PR               | `gh pr list --head <branch> --state all`       |
| Recently merged PRs                              | `gh pr list --state merged --limit 10`         |
| Read a PR in the terminal                        | `gh pr view <n>`                               |
| Read it with the discussion                      | `gh pr view <n> --comments`                    |
| Open it in the browser                           | `gh pr view <n> --web`                         |
| See the code changes                             | `gh pr diff <n>`                               |
| See CI results, or wait for them                 | `gh pr checks <n>`, `gh pr checks <n> --watch` |

#### Review and collaborate

| I want to…                | Command                                           |
| ------------------------- | ------------------------------------------------- |
| Test someone's PR locally | `gh pr checkout <n>`                              |
| Approve                   | `gh pr review <n> --approve`                      |
| Request changes           | `gh pr review <n> --request-changes --body "..."` |
| Leave a review comment    | `gh pr review <n> --comment --body "..."`         |
| Add a plain comment       | `gh pr comment <n> --body "..."`                  |

#### Update and finish

| I want to…                                | Command                                                                   |
| ----------------------------------------- | ------------------------------------------------------------------------- |
| Change title or body                      | `gh pr edit <n> --title "..." --body-file pr.md`                          |
| Add a reviewer, label or assignee         | `gh pr edit <n> --add-reviewer user --add-label bug --add-assignee "@me"` |
| Mark a draft ready for review             | `gh pr ready <n>`                                                         |
| Bring the branch up to date with its base | `gh pr update-branch <n>`                                                 |
| Squash-merge and delete the branch        | `gh pr merge <n> --squash --delete-branch`                                |
| Merge with a merge commit, or rebase      | `gh pr merge <n> --merge`, `gh pr merge <n> --rebase`                     |
| Merge automatically once checks pass      | `gh pr merge <n> --auto --squash`                                         |
| Close without merging, or reopen          | `gh pr close <n>`, `gh pr reopen <n>`                                     |

If merging to your main branch deploys something, treat `gh pr merge` as a release step and double-check the PR number first.



## gh issue: issues

The flags mirror `gh pr`, so once you know one group the other is easy.

| I want to…                                                        | Command                                                      |
| ----------------------------------------------------------------- | ------------------------------------------------------------ |
| List open issues                                                  | `gh issue list`                                              |
| Only those assigned to me                                         | `gh issue list --assignee "@me"`                             |
| Filter by label                                                   | `gh issue list --label bug`                                  |
| Issues assigned to me, mentioning me or opened by me in this repo | `gh issue status`                                            |
| Read one, with comments                                           | `gh issue view <n> --comments`                               |
| Create one                                                        | `gh issue create --title "..." --body "..."`                 |
| Create one with labels and an assignee                            | `gh issue create --title "..." --label bug --assignee "@me"` |
| Comment                                                           | `gh issue comment <n> --body "..."`                          |
| Edit labels or assignees                                          | `gh issue edit <n> --add-label urgent --add-assignee user`   |
| Close, or reopen                                                  | `gh issue close <n>`, `gh issue reopen <n>`                  |
| Start a branch linked to the issue                                | `gh issue develop <n> --checkout`                            |
| Pin, or transfer to another repo                                  | `gh issue pin <n>`, `gh issue transfer <n> OWNER/REPO`       |

`gh issue develop` creates a branch that GitHub links to the issue, so a PR from that branch shows up on the issue automatically.



## gh search and gh browse: finding things

`gh search` looks across all of GitHub, or one owner, using the same search syntax as the website. `gh browse` jumps from the terminal to the right page in your browser.

#### gh search

| I want to…                     | Command                                                     |
| ------------------------------ | ----------------------------------------------------------- |
| Find PRs across an org         | `gh search prs "login bug" --owner my-org`                  |
| Find my open PRs everywhere    | `gh search prs --author "@me" --state open`                 |
| Find PRs waiting for my review | `gh search prs --review-requested "@me" --state open`       |
| Find issues by label           | `gh search issues --label "good first issue" --language go` |
| Find repos                     | `gh search repos "static site generator" --stars ">1000"`   |
| Find code                      | `gh search code "useEffect" --repo OWNER/REPO`              |
| Find commits                   | `gh search commits "fix typo" --author user`                |

Inside one repo, `gh pr list --search "..."` and `gh issue list --search "..."` take the same syntax, e.g. `gh pr list --search "is:merged merged:>=2026-01-01"`.

#### gh browse

| I want to…                          | Command                                        |
| ----------------------------------- | ---------------------------------------------- |
| Open the repo home page             | `gh browse`                                    |
| Open a file                         | `gh browse src/app.js`                         |
| Open a file at a line               | `gh browse src/app.js:42`                      |
| Open a file on another branch       | `gh browse src/app.js --branch main`           |
| Open an issue or PR by number       | `gh browse <n>`                                |
| Open settings, wiki or projects     | `gh browse --settings`, `--wiki`, `--projects` |
| Print the URL instead of opening it | `gh browse src/app.js:42 --no-browser`         |

`--no-browser` is handy for pasting a link to an exact line into a chat or ticket.



## gh run and gh workflow: GitHub Actions

A **workflow** is the YAML file in `.github/workflows/`. A **run** is one execution of it. `gh workflow` manages the files, `gh run` follows the executions.

#### gh run

| I want to…                          | Command                                       |
| ----------------------------------- | --------------------------------------------- |
| See the latest runs                 | `gh run list --limit 10`                      |
| Runs for one branch or workflow     | `gh run list --branch main --workflow ci.yml` |
| Follow a run live until it finishes | `gh run watch`                                |
| Summary of one run                  | `gh run view <run-id>`                        |
| Only the logs of failed steps       | `gh run view <run-id> --log-failed`           |
| Re-run only the failed jobs         | `gh run rerun <run-id> --failed`              |
| Cancel a run                        | `gh run cancel <run-id>`                      |
| Download a run's artifacts          | `gh run download <run-id>`                    |

#### gh workflow

| I want to…                                           | Command                                                   |
| ---------------------------------------------------- | --------------------------------------------------------- |
| List workflows                                       | `gh workflow list`                                        |
| Start a workflow by hand (needs `workflow_dispatch`) | `gh workflow run deploy.yml --ref main`                   |
| Pass inputs to it                                    | `gh workflow run deploy.yml -f environment=staging`       |
| Open the workflow's page                             | `gh workflow view deploy.yml --web`                       |
| Turn a workflow off, or on                           | `gh workflow disable ci.yml`, `gh workflow enable ci.yml` |

`gh run view --log-failed` is the fastest way to see why CI went red without scrolling through a whole log.



## gh release and gh gist

#### gh release

| I want to…                               | Command                                              |
| ---------------------------------------- | ---------------------------------------------------- |
| List releases                            | `gh release list`                                    |
| Create a release with auto-written notes | `gh release create v1.2.0 --generate-notes`          |
| Create one and attach files              | `gh release create v1.2.0 dist/*.zip --notes "..."`  |
| Create a draft or pre-release            | `gh release create v1.3.0-beta --draft --prerelease` |
| Read a release                           | `gh release view v1.2.0`                             |
| Download its files                       | `gh release download v1.2.0`                         |
| Add files to an existing release         | `gh release upload v1.2.0 extra.zip`                 |
| Delete a release                         | `gh release delete v1.2.0`                           |

`--generate-notes` builds the release notes from the PRs merged since the previous release.

#### gh gist

| I want to…                            | Command                                         |
| ------------------------------------- | ----------------------------------------------- |
| Share a file as a secret gist         | `gh gist create notes.md`                       |
| Share it publicly, with a description | `gh gist create notes.md --public --desc "..."` |
| Share command output                  | `some-command \| gh gist create -`              |
| List my gists                         | `gh gist list`                                  |
| Read or edit one                      | `gh gist view <id>`, `gh gist edit <id>`        |

Secret gists are unlisted, not private: anyone with the link can open them, so keep secrets out.



## Getting exact data out: --json, --jq, --template

Most `list` and `view` commands can return JSON instead of a formatted screen. Ask for the fields you want with `--json`, then filter with `--jq` (`-q`) or format with `--template`. No separate `jq` install is needed: gh has it built in.

```bash
# See every field a command can return
gh pr list --json

# Pick fields
gh pr list --json number,title,author

# Print one value as plain text, e.g. to reuse an old PR description
gh pr view 123 --json body -q .body

# One line per PR: "#123 Fix login redirect"
gh pr list --json number,title -q '.[] | "#\(.number) \(.title)"'

# URLs of PRs with failing checks
gh pr list --json url,statusCheckRollup \
  -q '.[] | select(any(.statusCheckRollup[]; .conclusion == "FAILURE")) | .url'

# Go templates for a custom layout
gh pr list --json number,title --template '{{range .}}{{.number}}  {{.title}}{{"\n"}}{{end}}'
```

This is what makes gh useful in scripts: you get stable, structured output instead of text that changes between versions.



## Make gh your own: alias, config, api, extension

#### gh alias

| I want to…                                      | Command                                                                                                      |
| ----------------------------------------------- | ------------------------------------------------------------------------------------------------------------ |
| Make a shortcut                                 | `gh alias set mine 'pr list --author "@me"'`                                                                 |
| A shortcut that takes an argument               | `gh alias set prfor 'pr list --head "$1" --state all'`                                                       |
| A shell shortcut (here: check out my newest PR) | `gh alias set --shell co 'gh pr list --author "@me" --json number -q ".[0].number" \| xargs gh pr checkout'` |
| List or delete shortcuts                        | `gh alias list`, `gh alias delete mine`                                                                      |

After `gh alias set mine ...`, running `gh mine` lists your open PRs.

#### gh config

| I want to…                                 | Command                              |
| ------------------------------------------ | ------------------------------------ |
| Set the editor gh opens for bodies         | `gh config set editor "code --wait"` |
| Use SSH for clones                         | `gh config set git_protocol ssh`     |
| Turn off interactive prompts (for scripts) | `gh config set prompt disabled`      |
| See all settings                           | `gh config list`                     |

#### gh api

When no command exists for something, `gh api` calls the GitHub API directly, already signed in. `{owner}` and `{repo}` fill in from the current repo.

| I want to…                | Command                                                           |
| ------------------------- | ----------------------------------------------------------------- |
| Call a REST endpoint      | `gh api repos/{owner}/{repo}/branches`                            |
| Get every page of results | `gh api repos/{owner}/{repo}/issues --paginate`                   |
| Send data (POST)          | `gh api repos/{owner}/{repo}/issues -f title="Bug" -f body="..."` |
| Filter the response       | `gh api user -q .login`                                           |
| Run a GraphQL query       | `gh api graphql -f query='{ viewer { login } }'`                  |

#### gh extension

Extensions are community add-ons that become new gh commands.

| I want to…              | Command                                                                          |
| ----------------------- | -------------------------------------------------------------------------------- |
| Find extensions         | `gh extension browse` or `gh extension search dashboard`                         |
| Install one             | `gh extension install dlvhdr/gh-dash`                                            |
| List, upgrade or remove | `gh extension list`, `gh extension upgrade --all`, `gh extension remove gh-dash` |

Extensions run code on your machine with your GitHub login, so install only ones you trust.



## Habits worth copying

**Check before you create.** A branch may already have a PR, even a closed one. One command avoids a duplicate:

```bash
gh pr list --head "$(git branch --show-current)" --state all
```

**You don't need to switch branches to open a PR.** If the branch is pushed, `--head` opens its PR while your working copy stays where it is:

```bash
gh pr create --base main --head feature/new-search --fill
```

**Write long descriptions in a file, not a flag.** Quotes and line breaks inside `--body` get messy. `--body-file` takes a file, or `-` to read a heredoc:

```bash
gh pr create --fill --body-file - <<'EOF'
## Summary
- Adds dark mode toggle

## Testing
- [x] Checked in Chrome and Safari
EOF
```

Quoting `'EOF'` stops the shell from expanding `$` and backticks inside the text.

**Reuse a good description.** `gh pr view <n> --json body -q .body` prints an earlier PR's description, so a new one can follow the same layout.

**Mind the @ sign.** Writing `@name` in a PR, issue or comment notifies that GitHub user. Put package names like `@types/node` in backticks so nobody gets pinged by accident.

**Script with JSON, not screen output.** Anything you pipe into another tool should use `--json` and `--jq`. Formatted output can change between versions; JSON fields don't.

**Let help teach you.** When unsure of a flag, `gh <command> --help` is faster than a web search and always matches your installed version.



## Learn more

Start with the quickstart and the manual; the rest are for going further.

| Link                                                                                                                                                             | What it's good for                                               |
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- |
| <a href="https://docs.github.com/en/github-cli/github-cli/quickstart" target="_blank">GitHub CLI quickstart</a>                                                  | A short official tour, good first read                           |
| <a href="https://cli.github.com/manual/" target="_blank">gh manual</a>                                                                                           | Reference for every command and flag                             |
| <a href="https://cli.github.com/manual/gh_pr" target="_blank">gh pr manual</a>                                                                                   | Every pull request subcommand, with examples                     |
| <a href="https://cli.github.com/manual/gh_help_formatting" target="_blank">Formatting output</a>                                                                 | `--json`, `--jq` and `--template` in depth                       |
| <a href="https://jqlang.org/manual/" target="_blank">jq manual</a>                                                                                               | The filter language behind `--jq`                                |
| <a href="https://docs.github.com/en/search-github/searching-on-github/searching-issues-and-pull-requests" target="_blank">Searching issues and pull requests</a> | Everything you can put in `--search`                             |
| <a href="https://cli.github.com/manual/gh_alias_set" target="_blank">gh alias set</a>                                                                            | Shortcuts, arguments and shell aliases                           |
| <a href="https://cli.github.com/manual/gh_api" target="_blank">gh api</a>                                                                                        | Calling any GitHub API endpoint                                  |
| <a href="https://docs.github.com/en/rest" target="_blank">GitHub REST API</a> and <a href="https://docs.github.com/en/graphql" target="_blank">GraphQL API</a>   | The endpoints `gh api` can reach                                 |
| <a href="https://cli.github.com/manual/gh_extension" target="_blank">gh extension</a>                                                                            | Installing and writing extensions                                |
| <a href="https://github.com/topics/gh-extension" target="_blank">gh-extension topic</a>                                                                          | Browsing community extensions                                    |
| <a href="https://github.com/dlvhdr/gh-dash" target="_blank">gh-dash</a>                                                                                          | A popular extension: a terminal dashboard of your PRs and issues |
