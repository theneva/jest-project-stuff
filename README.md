# Jest project stuff

Jest ignores config and attempts to run all JS and JSON files as tests (ignoring the test config in the project folder) when only one project is specified in the `projects` array.

Repro:

0. Install dependencies: `yarn install`
1. Run `yarn jest`
