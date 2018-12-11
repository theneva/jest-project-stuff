# Jest project stuff

Jest attempts to run all JS, JSON, and snapshot files as tests (ignoring the test config in the project folder) when only one project is specified in the `projects` array.

Repro:

0. Install dependencies: `yarn install`
1. Run `yarn jest`

```sh-session
$ yarn

yarn install v1.12.3
[1/4] 🔍  Resolving packages...
warning Resolution field "yargs@12.0.0" is incompatible with requested version "yargs@^12.0.2"
warning Resolution field "yargs@12.0.0" is incompatible with requested version "yargs@^12.0.2"
success Already up-to-date.
✨  Done in 0.28s.

$ yarn jest

yarn run v1.12.3
$ /Users/theneva/code/jest-project-stuff/node_modules/.bin/jest
 PASS  packages/a/test.js
 FAIL  ./package.json
  ● Test suite failed to run

    Your test suite must contain at least one test.

      at node_modules/jest-cli/build/TestScheduler.js:256:22

 FAIL  packages/a/package.json
  ● Test suite failed to run

    Your test suite must contain at least one test.

      at node_modules/jest-cli/build/TestScheduler.js:256:22

 FAIL  packages/b/package.json
  ● Test suite failed to run

    Your test suite must contain at least one test.

      at node_modules/jest-cli/build/TestScheduler.js:256:22

Test Suites: 3 failed, 1 passed, 4 total
Tests:       1 passed, 1 total
Snapshots:   0 total
Time:        1.113s
Ran all test suites.
error Command failed with exit code 1.
info Visit https://yarnpkg.com/en/docs/cli/run for documentation about this command.
```

Interestingly, this project works if you change the `projects` field to also include `packages/b`, even though `packages/b` has no Jest config:

```diff
-      "packages/a"
+      "packages/a",
+      "packages/b"
```

```sh-session
$ yarn jest

yarn run v1.12.3
$ /Users/theneva/code/jest-project-stuff/node_modules/.bin/jest
 PASS   a  packages/a/test.js
  ✓ 1+1 (3ms)

Test Suites: 1 passed, 1 total
Tests:       1 passed, 1 total
Snapshots:   0 total
Time:        0.948s, estimated 1s
Ran all test suites.
✨  Done in 1.39s.
```

The same issue occurs if `projects` is defined with a wildcard. If `packages/b` is in place:

```diff
-      "packages/a"
+      "packages/*"
```

```sh-session
$ yarn jest

yarn run v1.12.3
$ /Users/theneva/code/jest-project-stuff/node_modules/.bin/jest
 PASS   a  packages/a/test.js
  ✓ 1+1 (3ms)

Test Suites: 1 passed, 1 total
Tests:       1 passed, 1 total
Snapshots:   0 total
Time:        1.013s
Ran all test suites.
✨  Done in 1.47s.
```

If you then delete `packages/b` so `packages/*` resolves to a single project:

```sh-session
$ git status

…

Changes not staged for commit:
  (use "git add/rm <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	deleted:    packages/b/package.json

no changes added to commit (use "git add" and/or "git commit -a")

$ yarn jest

yarn run v1.12.3
$ /Users/theneva/code/jest-project-stuff/node_modules/.bin/jest
 PASS  packages/a/test.js
 FAIL  ./package.json
  ● Test suite failed to run

    Your test suite must contain at least one test.

      at node_modules/jest-cli/build/TestScheduler.js:256:22

 FAIL  packages/a/package.json
  ● Test suite failed to run

    Your test suite must contain at least one test.

      at node_modules/jest-cli/build/TestScheduler.js:256:22

Test Suites: 2 failed, 1 passed, 3 total
Tests:       1 passed, 1 total
Snapshots:   0 total
Time:        1.128s
Ran all test suites.
error Command failed with exit code 1.
info Visit https://yarnpkg.com/en/docs/cli/run for documentation about this command.
```
