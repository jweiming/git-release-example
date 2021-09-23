# git-release-example

## Description
This is a simple exercise to cherry-pick commits for release planning. 

## Flow
1. Create the following files in the repo
```
$ touch file-a.txt
$ touch file-b.txt
$ touch file-c.txt
```

1. Stage files for commit
```
$ git add file-a.txt file-b.txt file-c.txt
```

1. Commit the changes with message "initial commit"
```
$ git commit -m "intiial commit"
[main f3a46a3] intiial commit
 3 files changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 file-a.txt
 create mode 100644 file-b.txt
 create mode 100644 file-c.txt
```

1. Push the changes to ```main``` branch
```
$ git push
Enumerating objects: 4, done.
Counting objects: 100% (4/4), done.
Delta compression using up to 4 threads
Compressing objects: 100% (2/2), done.
Writing objects: 100% (3/3), 291 bytes | 291.00 KiB/s, done.
Total 3 (delta 0), reused 0 (delta 0)
To github.com:jweiming/git-release-example.git
   8f6ef75..f3a46a3  main -> main
```

1. Create ```dev``` branch from main

```
$ git checkout -b dev
Switched to a new branch 'dev'
```

1. Check that you now have 2 branches

```
$ git branch
* dev
  main
```

1. Make the following changes to file-a.txt, file-b.txt, file-c.txt

```
$ echo "line 1" >> file-a.txt
$ echo "line a" >> file-b.txt
$ echo "line x" >> file-c.txt
```

1. Stage all files for commit

```
$ git add *
```

1. Commit the changes

```
$ git commit -m "updated file-a file-b file-c"
[dev cce9c81] updated file-a file-b file-c
 3 files changed, 3 insertions(+)
```

1. Push the new commits

```
$ git push --set-upstream origin dev
Enumerating objects: 9, done.
Counting objects: 100% (7/7), done.
Delta compression using up to 4 threads
Compressing objects: 100% (2/2), done.
Writing objects: 100% (5/5), 381 bytes | 381.00 KiB/s, done.
Total 5 (delta 0), reused 0 (delta 0)
remote:
remote: Create a pull request for 'dev' on GitHub by visiting:
remote:      https://github.com/jweiming/git-release-example/pull/new/dev
remote:
To github.com:jweiming/git-release-example.git
 * [new branch]      dev -> dev
Branch 'dev' set up to track remote branch 'dev' from 'origin'.
```
1. Create a release branch for v1.0 from ```master```

```
$ git checkout main
$ git checkout -b release/1.0 
```

1. Check what commits are available for cherry-pick in ```dev```

```
$ git checkout dev
$ git log --oneline
cce9c81 (HEAD -> dev, origin/dev) updated file-a file-b file-c
f3a46a3 (origin/main, origin/HEAD, release/1.0, main) intiial commit
8f6ef75 Initial commit
```

1. Cherry-pick the change ```cce9c81``` into ```release/1.0``` branch

```
$ git checkout release/1.0
$ git cherry-pick cce9c81
[release/1.0 5794f40] updated file-a file-b file-c
 Date: Thu Sep 23 09:48:54 2021 +0800
 3 files changed, 3 insertions(+)
```

1. Push the changes

```
git push --set-upstream origin release/1.0
```

1. Merge the release into ```main``` and tag it

```
$ git checkout main
$ git merge release/1.0
$ git tag v1.0
$ git push --tags
```

1. Make more changes 

```
$ echo "line 2" >> file-a.txt
$ echo "line 3" >> file-a.txt
$ git add file-a.txt
$ git commit -m "update file-a.txt"
$ echo "line b" >> file-b.txt
$ git add file-b.txt
$ git commit -m "update file-b.txt"
$ echo "line y" >> file-c.txt
$ echo "line z" >> file-c.txt
$ git add file-c.txt
$ git commit -m "update file-c.txt"
git push
```

1. Create release branch for ```1.1``` from ```1.0```

```
$ git checkout v1.0
$ git checkout -b release/1.1
```
1. Cherry pick only commits related to file-a.txt and file-b.txt for v1.1 release

```
$ git checkout dev
$ git log --oneline
8c44626 (HEAD -> dev, origin/dev) update file-c.txt
764cf19 update file-b.txt
1c79f85 update file-a.txt
cce9c81 updated file-a file-b file-c
f3a46a3 intiial commit
8f6ef75 Initial commit
$ git cherry-pick 1c79f85 764cf19
$ git push --set-upstream origin release/1.1
```
1. Merge the relase into ```main```

```
$ git checkout main
$ git merge release/1.1
$ git tag v1.1
$ git push --tags
```

1. Check what are the commits that are in ```dev```, but not yet released. We can see that commits related to file-c.txt are with a + prefix, which means it has not been cherrypicked for release

```
$ git cherry v1.1 dev -v
- cce9c81fde80f378fee5eeb6ba92ddc94fa567e3 updated file-a file-b file-c
- 1c79f85ea6359adfa3bb88b29405672a68a4470c update file-a.txt
- 764cf1910ca972d4a75bd58d5358464d67edbf1a update file-b.txt
+ 8c44626ca00f981064bfbe44288e4ce0787db408 update file-c.txt
```
1. Create new release branch for ```1.2``` from ```1.1

```
$ git checkout v1.1
$ git checkout -b release/1.2
$ git cherry-pick 8c44626ca00f981064bfbe44288e4ce0787db408
$ git push --set-upstream origin release/1.2
```

1. Merge release into ```main```

```
$ git checkout main
$ git merge release/1.2
$ git tag v1.2
$ git push --tags
```
