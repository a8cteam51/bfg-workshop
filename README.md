# BFG Workshop

With this workshop, you can get your hands-on experience using the [BFG Repo-Cleaner](https://rtyley.github.io/bfg-repo-cleaner/) tool.

The purpose of this repository is to simulate a real-life project in which sensitive information has been exposed to a public Github repository. In order to clean it up (besides removing the sensitive data with a new commit) the git history also needs to be rewritten, so that the exposed data is also removed from previous commits.

By following the steps below, you will learn how to deal with this situation and have some experience for future events.

## Before you start

1. Install BFG
	- brew install bfg
	- or, manually install it by downloading the .jar from the page.

2. Fork this repository to your personal Github account so you can have your own sandbox to poke around.

3. Clone your own repo, not this one.

## The current state of the project

There's an `.env` file that has been commited, exposing sensitive credentials to a production database. This file needs to be completely removed from the git history.

Additionally, there's another file `settings.json` that contains an API key. This file should not be deleted, but that API key needs to be removed, and obfuscated on the git history.

_

### Step 1: Check out the list of commits.
Give yourself an idea of what has been commited by visiting the URL `/bfg-workshop/commits/trunk` on your Github sandbox repo.

You will notice a few commits that start with the text _"Whoops!"_. Those files are the ones we are going to work with.

Commits: [9a6e41b](https://github.com/a8cteam51/bfg-workshop/commit/9a6e41b5de39a79d78f30da70242409841dd304c), [44809a7](44809a79582835cd794184546435d58cdf9fe63c)  

_

### Step 2: Delete file.

In this step we'll focus on removing the .env file we mistakenly committed to the branch along with it's commit history.

First remove the file from your local repo and commit and push the changes to the remote repo.
- Do this with `git rm .env`, then commit and push as normal.
After the push, you'll notice the .env file is no longer in the repo on Github, however it and it's contents are still visible in some of the previous commits. Let's remove that history.

Remove the commit history:
- Use the following command: `bfg --delete-files .env`. The command will run, providing details of the clean-up operation, followed by the statement `BFG run is complete! When ready, run: git reflog...`.
- Copy the command from the terminal and run it. (For reference, the command should be `git reflog expire --expire=now --all && git gc --prune=now --aggressive`).
- Now force a push to the remote repository using `git push --force`.

The file should now be removed from all commit history both locally, and in the remote repository. See Step 4 below for additional important information.

–
### Step 3: Obfuscate text.

Now lets focus on how to remove a string from the `settings.json` file without actually removing the whole file from the history.
- First remove the offending text in the `settings.json` file by removing the value from the string, leaving an empty string is fine. Commit and push the change to the remote repository.
- Create a file called `replacements.txt` in the git root directory and populate it with the text that you want to obfuscate, one string per line. In our case, `replacements.txt` will only contain one single line:
```
07dba36e-0506-4230-ba5b-4e2fa87c546d==>0000
```
This replaces the API string with `0000`
- From the Terminal and in the git root directory, run `bfg --replace-text replacements.txt -fi '*.json'`. If you're in a different folder use the format `bfg --replace-text ~/path/to/replacements.txt -fi '*.json'` (replace `~/path/to/` with the actual path).
- On screen, check the files that are going to be changed. Make sure that only the files that you want will be modified. 
- Do a `git reflow ...` by following the command provided in your Terminal, the same as in Step 2 above.
- Execute `git push -f` (same as `git push --force`).

The API key value should now be removed from all commit history both locally and in the remote repository.

–
### Step 4: Avoid pushing from old branches.
- In a real-world scenario, you should let the team know that the scrubbing process is done and they should delete their local branches and re-clone from the cleaned remote repo. Failure to do this could cause the removed history to be re-introduced. 
- Additionally, while the content/file is removed, linking directly to the original commit (eg `github.com/username/repo-name/commit/652ac....194c`) will still show the content or file even though the commit is no longer included in our repo history, and anyone forking or cloning the repo won’t have this history or any reference to it. It is for this reason that any secrets that are committed to Github should be considered compromised, even if removed. 

---

## Resources

- BFG Repo Cleaner:   
https://rtyley.github.io/bfg-repo-cleaner/

- Scrubbing a repo clean:   
https://fieldguide.automattic.com/scrubbing-a-repo-clean/

- Sanitizing Repository History Using Tower and the BFG:   
https://fieldguide.automattic.com/sanitizing-repository-history-using-tower-and-the-bfg/

- Removing sensitive data from a repository:
https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/removing-sensitive-data-from-a-repository

- How to substitute text:   
https://stackoverflow.com/a/15730571/1940238


```
bfg 1.14.0
Usage: bfg [options] [<repo>]

  -b, --strip-blobs-bigger-than <size>
  -B, --strip-biggest-blobs NUM
  -bi, --strip-blobs-with-ids <blob-ids-file>
  -D, --delete-files <glob>
  --delete-folders <glob>
  --convert-to-git-lfs <value>
  -rt, --replace-text <expressions-file>
  -fi, --filter-content-including <glob>
  -fe, --filter-content-excluding <glob>
  -fs, --filter-content-size-threshold <size>
  -p, --protect-blobs-from <refs>
  --no-blob-protection
  --private
  --massive-non-file-objects-sized-up-to <size>

https://repository.sonatype.org/service/local/repositories/central-proxy/content/com/madgag/bfg/1.14.0/bfg-1.14.0.txt
```