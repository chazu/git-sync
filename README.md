# git-sync

sychronize tracking repositories

2012 by Simon Thum

This scrips intends to sync near-automatically via git 
in "tracking" repositories where a nice history is not
as crucial as having one.

Licensed under CC0

## One more git sync script? Seriously?

Unlike the myriad of scripts to do just that already available,
it follows the KISS principle: It is safe, small, requires nothing but
git and bash, but does not even try to shield you from git.

It is ultimately intended for git-savy people. As a rule of thumb, if
you know how to complete a failed rebase you're fine.

Tested on msysgit and a real bash. In case you know bash scripting, it
will probably make your eyes bleed, but for some reason it works.

### What does it do?

It will likely get from you from a dull normal git repo with trivial
changes to an updated dull normal git repo equal to origin. No more,
no less.

Care has been taken that any kind of problem, pre-existing or not,
results in clear error messages and non-zero return code, but of
course no guarantee can be given.

The intent is to do everything that's needed to sync
automatically, and resort to manual intervention as soon
as something non-trivial occurs. It is designed to be safe
in that `git-sync` will likely refuse to do anything not known to
be safe.

### How am I supposed to use it?

Just call `git-sync` inside your average joe's repository (not in the
middle of a rebase, git-am, merge or whatever, not detached, no
untracked files) and everything will likely just work. Else, a clear
error message should appear. If you don't sync in an intertwined
manner (from multiple repositories/machines), `git-sync` is virtually
guaranteed to work.

## How does it work?

The flow is roughly:

1. sanity checks. You don't want to do this in the middle of a rebase.
2. Check for new files; exit if there are, unless allowed in config.
3. Check for auto-commitable changes (see syncNewFiles option).
4. perform auto-commit
5. one more check for leftover changes / general tidyness
6. fetch upstream
7. Relate upstream to ours. If ahead, push. If behind, pull. If diverged, rebase, then push.
6. At exit, assert sync state once more just to be safe.

On the first invocation, `git-sync` will ask you to whitelist the
current branch for sync using git config. This has to be done once for
every repository (and branch, for completeness).

## Options

There are two `git config`-based options for tailoring your sync:

    branch.$branch_name.syncNewFiles (bool)
    
Tells git-sync to invoke auto-commit even if new (untracked) files are
present. Normally you have to commit those yourself to prevent
accidential additions. git-sync will exit at stage 3 with an
explanation in that case.

    branch.$branch_name.autocommitscript (string)
	
A string which is being eval'ed by this script to perform an
auto-commit. Here you can run a commit script which should not
leave any uncommited state. The default will commit modified or
all files with a more or less useful message.

By default, commit is done using:

    git add -u ; git commit -m "changes from $(uname -n) on $(date)"

Or if you enable `syncNewFiles`:

    git add -A ; git commit -m \"changes from $(uname -n) on $(date)\";"

# License

I declare this work to be useable under the provisions of the CC0 license.

http://creativecommons.org/publicdomain/zero/1.0/

Attribution is appreciated, but not required.

# Thanks

Thanks go to all the people behind git.
