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
[TBD]

–
### Step 3: Obfuscate text.

Now lets focus on how to remove a string from the `settings.json` file without actually removing the whole file from the history.
- Create a  file called `replacements.txt` and populate it with the text that you want to obfuscate, one string per line. In our case, `replacements.txt` will only contain one single line:
```
07dba36e-0506-4230-ba5b-4e2fa87c546d==>0000
```
This replaces the API string with `0000`
- From the Terminal, run `bfg --replace-text ~/path/to/replacements.txt -fi '*.json'` 
- On screen, check the files that are going to be changed. Make sure that only the files that you want will be modified.
- Do a `git reflow` by following the steps on your Terminal
- Execute `git push -f`

–
### Step 4: Avoid to push old branches.
In a real-world scenario, you should let the team know that the scrubbing process is done and that they should do a `git pull` and either:
- Delete their local branches
- Rebase them from the updated `trunk`

---

## Resources

- BFG Repo Cleaner:   
https://rtyley.github.io/bfg-repo-cleaner/

- Scrubbing a repo clean:   
https://fieldguide.automattic.com/scrubbing-a-repo-clean/

- Sanitizing Repository History Using Tower and the BFG:   
https://fieldguide.automattic.com/sanitizing-repository-history-using-tower-and-the-bfg/

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