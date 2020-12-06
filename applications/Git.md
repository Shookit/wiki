Git
===

Rebasing
--------

```bash
# Will always use upstream/master's diff during merge conflicts
git rebase -s recursive -X ours upstream/master

# Will always use current diff during merge conflicts
git rebase -s recursive -X theirs upstream/master

# Interactive rebase (merge, reorder, drop, or reword)
git rebase -i HEAD~10
```


Origins
-------

### Add an origin

```
git remote add origin login@IP/path/to/repository
```

### Multiple origins

```
[remote "origin"]
    url = origin-host:path/proj.git
    url = duostack-host:path/proj.git
```

Snippets
--------

### Remove file from all commits

```
git filter-branch -f --tree-filter 'rm -rf badfile.txt' HEAD
```

### Shared repository w multiple users

```
git --bare init --shared=group
```

### Delete remote branch

```
git push origin --delete <branchName>
```

### Tagging

```bash
git tag tagname
git push --tags
```

### Stash

```bash
git stash show -p stash@{0}
git stash pop
git stash drop
git diff stash@{0}
```

Search all commits
------------------

```
git log -S password

# Show diffs
git log -p -S password
```

Don't track file (w/o gitignore)
--------------------------------

```
git update-index --assume-unchanged FILE_NAME
```

