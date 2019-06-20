Before updating the get started guides make sure the following
settings are present in your `.gitconfig`:

```
[alias]
    ll = log --color --graph --pretty=format:'%C(yellow)%h%Creset -%C(bold yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit
    l = log --color --graph --pretty=format:'%C(yellow)%h%Creset -%C(bold yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit -n20

[rebase]
    autosquash = true
```

The following steps to update the get started guides must be done for each repository. Like bpmn20, dmn11 and so on.

- At first clone the corresponding get started repository which should be updated. If you already clone the repository before fetch all of the changes done via the jobs + the correct tags:
```
git fetch --tags
git reset --hard origin/master
```
- to make sure you have all the commits and tags
```
git ll
git l Bonus # for camunda-get-started-spring repository
```
- Test the repository by adjusting the minor camunda bpmn version in the `pom.xml`.
- If everything works after releasing the artifacts you can simply trigger the following jenkins job, keep in mind to adjust the versions of it:
https://release.cambpm.camunda.cloud/job/maintenance/job/update-get-started-poms/

# In case manual changes are needed
First pull all of the latest changes
```
git fetch --tags
git reset --hard origin/master
```

Then you need to change whatever you want, fixup the commit (HASH) which first introduced the changed file and force push the changes:
```
git ll
git commit -a --fixup=HASH
git rebase -i --autosquash --root
git push --force-with-lease
```

As last step run the job to update the tags

# Repository camunda-get-started-spring and bonus tag

If you want to need to change something on camunda-get-started-spring repo, don't forget the Bonus tag. 
```
git checkout Bonus
git commit -a --fixup=HASH
git rebase -i --autosquash --root
./tag.sh
git push --tags --force

git checkout master
git reset --hard Bonus~
git push --tags --force origin master
```

tag.sh:
```
#!/bin/bash
    git tag -l -n1 | while read -r tag; do
        name=$(echo ${tag} | cut -d ' ' -f 1)
        msg=$(echo ${tag} | cut -d ' ' -f 2-)
        hash=$(git rev-list --pretty=oneline HEAD | grep "${msg}" | cut -d ' ' -f 1)

        if [ -n "${hash}" ]; then
            git tag -f -a "${name}" -m "${msg}" $hash
        fi
    done
```

# Old procedure
- Check out on which commit the pom.xml was first created. We use the corresponding commit hash
for the next steps as `HASH`.
- Commit the changes as fixup for the commit on which the `pom.xml` was added.
In most cases it will be the commit which is tagged as `Step-1`.
- Rebase the commit in interactive mode.
The `HASH~1` should be the commit hash which was before the commit, on which the pom.xml was added.
If the autosquash is present in the `.gitconfig` the rebase orders the commits in the opened editor.
The editor can simply be closed. The changes are now propagated to the other commits.
(Please note, if you need to change the Start tag commit, you will need to use `--root` instead of `HASH-1`.)
```
  $ vim pom.xml
  $ git ll
  $ git commit -a --fixup=HASH
  $ git rebase -i HASH~1
```
- Overwrite the existing tags with the new commit hashs. Since the commits are rebased the commit hashes are changed, too. So use for the next step `git ll` to
get the actual hashes. The tags must be overridden with the new commits and changes.
For this you can use the following script:
```sh
#!/bin/bash

git tag -l -n1 | while read tag; do
    name=$(echo $tag | cut -d " " -f 1)
    msg=$(echo $tag | cut -d " " -f 2-)
    echo "$name | $msg"
    echo -n "Which commit hash to use: "
    read hash < /dev/tty
    git tag -f -a $name -m "$msg" $hash
done
```
The script asks for each tag after a corresponding commit hash.
So each tag can be reassigned to a new commit. The old messages are reused.

Alternatively you can do it per hand for each tag with the following command:
```
  $ git tag -f -a "TAG_NAME" -m "TAG_MESSAGE" NEW_COMMIT_HASH
```
After the execution the tags must be **force** pushed.
```sh
$ git ll
$ ./tag.sh
$ git push --tags --force origin master
```