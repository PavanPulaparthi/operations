---
title: Galaxy Upgrading procedures
---


# 7 days before downtime

- Write an announcment about the potential Galaxy downtime explaining that Galaxy is being upgraded. Be sure to link to the release annoucement.

# 1 day before downtime

Note: this is the way using `git rebase` see below for a different approach.

1. Clone [our fork](https://github.com/usegalaxy-eu/galaxy/).
2. Check out the release branch you want to switch to, e.g. `release_XX.ZZ`
3. Ensure it's updated: `git pull`
4. Checkout *our* previous release branch (`release_XX.YY`)
5. `git rebase -i release_XX.ZZ` to rebase our commits on top of the
7. Update [`infrastructure-playbook`](https://github.com/usegalaxy-eu/infrastructure-playbook/) to sync configuration files and PR this + latest commit ID of the new branch
8. `make client-production`
9. `python scripts/plugin_staging.py` (if it exists)
9. `git add -f static/`
10. `git commit -a -m 'CLIENTBUILD'`
11. `git push -f`

If you're rebasing against an updated release branch (because you branched much earlier) then you'll want to do a `git rebase -i release_XX.YY` and **drop** any 'CLIENTBUILD' type commits, before doing another round of client-build / plugin staging / adding static / committing.

A different way to handle this is to use patches as follows:

1. Clone [our fork](https://github.com/usegalaxy-eu/galaxy/).
2. Check out the release branch you want to switch to, e.g. `release_XX.ZZ`
3. Ensure it's updated: `git pull`
4. Checkout *our* previous release branch (`release_XX.YY`)
5. `git rebase -i release_XX.ZZ` to rebase our commits on top of the
7. Update [`infrastructure-playbook`](https://github.com/usegalaxy-eu/infrastructure-playbook/) to sync configuration files and PR this + latest commit ID of the new branch
8. `make client-production`
9. `python scripts/plugin_staging.py` (if it exists)



# Downtime begins

- (optionally) update conda with

```bash
galaxy@sn04:~$ export PATH=/usr/local/tools/_conda/bin/:$PATH
galaxy@sn04:~$ which conda
/usr/local/tools/_conda/bin/conda
galaxy@sn04:~$ conda update -n base -c conda-forge conda
```

- Run playbook (maybe with `make galaxy CHECK=1` to be certain of your changes.)
- Add a blog post about this (an [example](https://github.com/usegalaxy-eu/galaxy-freiburg/pull/82))

# Rebuilding the client

```
make client-production
git add -f static/
git commit -a -m 'CLIENTBUILD'
git push -f
```
