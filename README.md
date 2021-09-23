# git-release-example

## Description
This is a simple exercise to cherry-pick commits for planning releases. 

## Flow

We will be working on three text files and making some changes to the contents of them. At various points, we will try to create releases from these changes using ```cherry-pick``` to select the commits we want. In the final version in this exercise, we will be checking for unreleased commits that and include them for a final ```v1.2``` release.

## Steps

### Initial Commit

1. Create the following files in the repo
```
$ touch file-a.txt
$ touch file-b.txt
$ touch file-c.txt
```
2. Stage files for commit
```
$ git add file-a.txt file-b.txt file-c.txt
```
3. Commit and push the changes with message "initial commit"
```
$ git commit -m "intiial commit"
$ git push
```
### Working on Dev Branch

1. Create ```dev``` branch from main
```
$ git checkout -b dev
```
2. Check that you now have 2 branches
```
$ git branch
* dev
  main
```
3. Make the following changes to file-a.txt, file-b.txt, file-c.txt
```
$ echo "line 1" >> file-a.txt
$ echo "line a" >> file-b.txt
$ echo "line x" >> file-c.txt
```
4. Commit and push your changes
```
$ git add *
$ git commit -m "updated file-a file-b file-c"
$ git push --set-upstream origin dev
```

### Creating Release v1.0
1. Create a release branch for v1.0 from ```master```
```
$ git checkout main
$ git checkout -b release/1.0 
```
2. Check what commits are available for cherry-pick in ```dev```
```
$ git checkout dev
$ git log --oneline
cce9c81 (HEAD -> dev, origin/dev) updated file-a file-b file-c
f3a46a3 (origin/main, origin/HEAD, release/1.0, main) intiial commit
8f6ef75 Initial commit
```
3. Cherry-pick the change ```cce9c81``` into ```release/1.0``` branch
```
$ git checkout release/1.0
$ git cherry-pick cce9c81
[release/1.0 5794f40] updated file-a file-b file-c
 Date: Thu Sep 23 09:48:54 2021 +0800
 3 files changed, 3 insertions(+)
```
4. Push the changes

```
git push --set-upstream origin release/1.0
```

5. Merge the release back into ```main``` and tag

```
$ git checkout main
$ git merge release/1.0
$ git tag v1.0
$ git push --tags
```

### Creating Release v1.1

1. Commit and push additional changes to the files

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

2. Create release branch for ```1.1``` from ```1.0```

```
$ git checkout v1.0
$ git checkout -b release/1.1
```
3. Cherry pick only commits related to ```file-a.txt``` and ```file-b.txt``` for v1.1 release

```
$ git checkout dev
$ git log --oneline
8c44626 (HEAD -> dev, origin/dev) update file-c.txt
764cf19 update file-b.txt
1c79f85 update file-a.txt
cce9c81 updated file-a file-b file-c
f3a46a3 intiial commit
8f6ef75 Initial commit
$ git checkout release/1.1
$ git cherry-pick 1c79f85 764cf19
$ git push --set-upstream origin release/1.1
```
4. Merge the relase back into ```main```

```
$ git checkout main
$ git merge release/1.1
$ git tag v1.1
$ git push --tags
```

### Creating Release v1.2

1. Now we will release all remaining commits.

2. Check what are the commits that are in ```dev```, but not yet released. We can see that commits related to file-c.txt are with a + prefix, which means it has not been cherrypicked for release
```
$ git cherry v1.1 dev -v
- cce9c81fde80f378fee5eeb6ba92ddc94fa567e3 updated file-a file-b file-c
- 1c79f85ea6359adfa3bb88b29405672a68a4470c update file-a.txt
- 764cf1910ca972d4a75bd58d5358464d67edbf1a update file-b.txt
+ 8c44626ca00f981064bfbe44288e4ce0787db408 update file-c.txt
```
3. Create new release branch for ```1.2``` from ```1.1 and push the changes
```
$ git checkout v1.1
$ git checkout -b release/1.2
$ git cherry-pick 8c44626ca00f981064bfbe44288e4ce0787db408
$ git push --set-upstream origin release/1.2
```
4. Merge release back into ```main``` and tag
```
$ git checkout main
$ git merge release/1.2
$ git tag v1.2
$ git push --tags
```
## Conclusion
You have completed the exercise and you now know the various ```git``` commands to help you prepare for a release.
