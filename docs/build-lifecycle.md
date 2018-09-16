# Build Lifecycle

Cement's lifecycle is similar to native [Travis CI lifecyle steps](https://docs.travis-ci.com/user/customizing-the-build/#the-build-lifecycle):

1. `before_install`
2. `install`
3. `before_script`
4. `script`
5. `after_success`* or `after_failure`*
6. `before_deploy`*
7. `deploy`*
8. `after_deploy`*
9. `rollback`*
10. `after_script`

<small>* Denotes an optional step that runs conditionally.</small>

Individual projects have traditionally been managed in different repositories.  A Travis CI build lifecycle is designed to provide different stages to organize and script out build and deployment steps for the project.

Projects managed in a monorepo require a slightly different build lifecycle to provide similar functionality with the added benefits of managing multiple projects together in a single pipeline.  This is why Cement introduces the concept of running lifecycle steps relative to a project or the entire repository.

Every step in the lifecycle is run three times:

1. Before all projects from the root of the repository.
2. For each project from the root of each project.
3. After all projects from the root of the repository.

The full, enumerated lifecycle looks like this:

|    | Repository Context   | Project Context  | Occurs                                                                      | Condition                                                                 |
|----|----------------------|------------------|-----------------------------------------------------------------------------|---------------------------------------------------------------------------|
| 1  | `before_install`     |                  | Before all project `before_install` steps.                                  | Always                                                                    |
| 2  |                      | `before_install` | For each project.                                                           | Always                                                                    |
| 3  | `before_install`     |                  | After all project `before_install` steps have finished.                     | Always                                                                    |
| 4  | `install`            |                  | Before all project `install` steps.                                         | Always                                                                    |
| 5  |                      | `install`        | For each project.                                                           | Always                                                                    |
| 6  | `install`            |                  | After all project `install` steps have finished.                            | Always                                                                    |
| 7  | `before_script`      |                  | Before all project `before_script` steps.                                   | Always                                                                    |
| 8  |                      | `before_script`  | For each project.                                                           | Always                                                                    |
| 9  | `before_script`      |                  | After all project `before_script` steps have finished.                      | Always                                                                    |
| 10 | `script`             |                  | Before all project `script` steps.                                          | Always                                                                    |
| 11 |                      | `script`         | For each project.                                                           | Always                                                                    |
| 12 | `script`             |                  | After all project `script` steps have finished.                             | Always                                                                    |
| 13 | `after_success`      |                  | Before all project `after_success` and `after_failure` steps have finished. | All projects' `script` steps succeeded.                                   |
| 13 | `after_failure`      |                  | Before all project `after_success` and `after_failure` steps have finished. | Any project's `script` step failed.                                       |
| 14 |                      | `after_success`  | For each project.                                                           | Corresponding project's `script` step succeeded.                          |
| 14 |                      | `after_failure`  | For each project.                                                           | Corresponding project's `script` step failed.                             |
| 15 | `after_success`      |                  | After all project `after_success` and `after_failure` steps have finished.  | All projects' `script` steps succeeded.                                   |
| 15 | `after_failure`      |                  | After all project `after_success` and `after_failure` steps have finished.  | Any project's `script` step failed.                                       |
| 16 | `before_deploy`      |                  | Before all project `before_deploy` steps.                                   | All projects' `script` step succeeded.                                    |
| 17 |                      | `before_deploy`  | For each project.                                                           | All projects' `script` step succeeded.                                    |
| 18 | `before_deploy`      |                  | After all project `before_deploy` steps have finished.                      | All projects' `script` step succeeded.                                    |
| 19 | `deploy`             |                  | Before all project `deploy` steps.                                          | All projects' `before_deploy` step succeeded.                             |
| 20 |                      | `deploy`         | For each project.                                                           | All projects' `before_deploy` step succeeded.                             |
| 21 | `deploy`             |                  | After all project `deploy` steps have finished.                             | All projects' `before_deploy` step succeeded.                             |
| 22 | `after_deploy`       |                  | Before all project `after_deploy` steps.                                    | All projects' `deploy` step succeeded.                                    |
| 23 |                      | `after_deploy`   | For each project.                                                           | All projects' `deploy` step succeeded.                                    |
| 24 | `after_deploy`       |                  | After all project `after_deploy` steps have finished.                       | All projects' `deploy` step succeeded.                                    |
| 25 | `rollback`           |                  | Before all project `rollback` steps.                                        | Any project's `deploy` step failed after at least one `deploy` succeeded. |
| 26 |                      | `rollback`       | For each project whose `deploy` step succeeded.                             | Any project's `deploy` step failed after at least one `deploy` succeeded. |
| 27 | `rollback`           |                  | After all project `rollback` steps have finished.                           | Any project's `deploy` step failed after at least one `deploy` succeeded. |
| 28 | `after_script`       |                  | Before all project `after_script` steps.                                    | The build progressed past `install`.                                      |
| 29 |                      | `after_script`   | For each project.                                                           | The build progressed past `install`.                                      |
| 30 | `after_script`       |                  | After all project `after_script` steps have finished.                       | The build progressed past `install`.                                      |
