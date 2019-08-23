# Releasing external task client JS

## Preconditions
Before starting the release procedure, make sure that:
 - all [Tests](https://travis-ci.org/camunda/camunda-external-task-client-js/branches) on master pass
 - New features are documented in [/docs](https://github.com/camunda/camunda-external-task-client-js/tree/master/docs)
 - Talk to Thorben if we want to publish a blogpost along with it

## Publish to Github
1. Note new Features and bugfixes in `CHANGELOG.md`
Template:
```
## 1.x.x
### Features
- Feature 1

### Bug Fixes
- Bugfix 1
```

2. Adjust the Version in `package.json`
```diff
- "version": "1.x.x-SNAPSHOT",
+ "version": "1.x.x",
 "name": "camunda-external-task-client-js",
 "main": "index.js",
```

3. run `npm i` to update the `package-lock.json`
4. Commit, Tag and push repository
 ```
 git commit -a -m "chore(release): release version 1.x.x"
 git tag v1.x.x.
 git push origin master --tags
 ```
4. Copy the release notes from step 1 to the newly created [github release](https://github.com/camunda/camunda-external-task-client-js/releases)

## Publish to NPM
Camunda external task client JS is an NPM package; we will not build it but publish it on NPM.
> Make sure you finished the release on GitHub first!

 1. make sure you checked out the correct version
 `git checkout v1.x.x`
 2. login to npm on your command line using the [credentials from confluence](https://app.camunda.com/confluence/display/camBPM/NPM)
 3. Push to npm
 `npm publish`
 If you see no error in the console, everything went right. Check the result [here](https://www.npmjs.com/package/camunda-external-task-client-js)
 > When publishing an alpha, tag it as such (`npm publish --tag alpha`). It can then be installed using `npm install camunda-external-task-client-js@alpha`. 

## Cleanup
Set the Version on master to the next-highest version snapshot, e.g.
```diff
- "version": "1.x.0",
+ "version": "1.y.0-SNAPSHOT",
 "name": "camunda-external-task-client-js",
 "main": "index.js",
```

---

If you released a new minor version, update the compatibility matrix in the docs: https://github.com/camunda/camunda-docs-manual/blob/master/content/user-guide/ext-client/compatibility-matrix.md

Either release the docs yourself or wait for the next scheduled release to publish the changes. Changes for already released Versions always have to be triggered manually in Jenkins (change Release version): https://ci.cambpm.camunda.cloud/job/docs/job/camunda-docs-manual-stage%20(7.11,%207.11)/
