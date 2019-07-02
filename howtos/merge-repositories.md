# Merging Repositories

Some helpful `git` commands to help you with merging a whole repository, or just a subfolder, into another repository. You can execute the first part of the commands as a `bash` script and do the actual merge manually since a commit message will need to be provided.

## Merge a subfolder of repository A into a subfolder of repository B

```bash
#!/bin/bash

# GitHub username
username='camunda'

# repo to be merged
repoA='camunda-commons'

# repo to merge in
repoB='camunda-bpm-platform'

# new directory name of repo A in repo B directory structure
dirName='typed-values'

# merge branch for repo A
mergeBranch='to-be-merged'

# remote for repo A in repo B
remoteName=$repoA

# clone both repositories
git clone https://github.com/$username/$repoA
git clone https://github.com/$username/$repoB

# go to the repository that contains the folder to be merged
cd $repoA

# create a merge branch
git checkout -b $mergeBranch

# Filter out and keep all the commits related to $dirName 
git filter-branch --prune-empty --tag-name-filter cat --subdirectory-filter $dirName $mergeBranch --all

# The previous step will remove the actual directory to be kept. 
# To bring it back, or rename it, we need to run the following command. It will also preserve and update the tags
git filter-branch -f --tag-name-filter cat --tree-filter 'mkdir -p $dirName; mv -t $dirName * .* || true'

# (Optional) Clean up the repository
git reset --hard
git gc --aggressive
git prune
git clean -df

# move to the repository to merge to
cd ../$repoB

# add a remote to the local path of repo A (the original directory, not the sub-folder)
git remote add $remoteName ../$repoA
```

Do the following manually, since a commit message will need to be added
```bash
# Merge repo A with repo B. This will result with a merge commit. 
# Give the merge commit a meaningful message (like chore(engine): merge dmn-engine repo into platform)
git pull --allow-unrelated-histories $remoteName $mergeBranch

# You can check the result in a nice way like this
git log --oneline --graph
```

## Merge a whole repository A in a subfolder of repository B

```bash
#!/bin/bash

# GitHub username
username='camunda'

# repo to be merged
repoA='camunda-engine-dmn'

# repo to merge in
repoB='camunda-bpm-platform'

# new directory name of repo A in repo B directory structure
newDirName='engine-dmn'

# merge branch for repo A
mergeBranch='to-be-merged'

# remote for repo A in repo B
remoteName=$repoA

#clone both repositories
git clone https://github.com/$username/$repoA
git clone https://github.com/$username/$repoB

# go to the repo to be merged
cd $repoA

# create a merge branch
git checkout -b $mergeBranch

# Rewrite commits of repo A such that all changes are applied in the desired subfolder. 
# During the rewrite, git will complain that it cannot move a directory to a subdirectory of itself, you can ignore this.
git filter-branch --tag-name-filter cat --tree-filter 'mkdir -p $dirName; mv -t $dirName * .* || true'

# move to repo B
cd ../$repoB

# add a remote to the local path of repo A (the original directory, not the sub-folder)
git remote add $remoteName ../$repoA
```

Do the following manually, since a commit message will need to be added
```bash
# Merge repo A with repo B. This will result with a merge commit. 
# Give the merge commit a meaningful message (like chore(engine): merge dmn-engine repo into platform)
git pull --allow-unrelated-histories $remoteName $mergeBranch

# You can check the result in a nice way like this
git log --oneline --graph
```

## Resources

- [GitHub](https://help.github.com/en/articles/splitting-a-subfolder-out-into-a-new-repository)
- [Zeebe](https://github.com/zeebe-io/zeebe/wiki/Merge-repositories)
- [mattsch](https://mattsch.com/2015/06/19/move-directory-from-one-repository-to-another-preserving-history/)