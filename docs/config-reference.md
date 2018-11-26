# Config Reference

* [Base Configuration (`.cement.yml`)](#base-configuration)
  + [`after_all`](#after_all)
  + [`before_all`](#before_all)
  + [`for_each`](#for_each)
  + [`ignore`](#ignore)
  + [`projects_root`](#projects_root)
* [Override Configuration (`.cementover.yml`)](#override-configuration)
  + [`after_deploy`](#after_deploy)
  + [`after_failure`](#after_failure)
  + [`after_script`](#after_script)
  + [`after_success`](#after_success)
  + [`before_deploy`](#before_deploy)
  + [`before_install`](#before_install)
  + [`before_script`](#before_script)
  + [`deploy`](#deploy)
  + [`ignore`](#ignore)
  + [`install`](#install)
  + [`override`](#override)
  + [`rollback`](#rollback)
  + [`script`](#script)
* [Common Structures](#common-structures)
  + [Conditional](#conditional)
  + [Deployment](#deployment)
* [Environment Variables](#environment-variables)

## Base Configuration

The base configuration is stored in a `.cement.yml` file in the root of a monorepo.

----------

### `after_all`

Defines build stages to run after all their project-level counterparts have completed.

----------

#### `after_all: after_deploy`

Build step that runs after all project-level `after_deploy` steps have finished.  This build step only runs if all project-level and repository-level `deploy` steps succeeded.  If `after_deploy` fails, the build jumps to `rollback`, and the build is considered failed.

__Possible values:__

* **_nil_**: take no action.

* __true__: disable this build step.

* __ShellCommand__: a shell command to execute.

* __ShellCommand[]__: an array of shell commands to execute.  All commands are executed in parallel.  If any command fails (exits with value > 0), all outstanding commands are allowed to complete before rollback logic is executed.

* __Conditional[]__: an array of [Conditional](#conditional) objects.

__Default value:__ _nil_

__Examples:__

```yml
after_all:
  after_deploy: echo "Deployment successful."
```

```yml
after_all:
  after_deploy:
    - echo "Deployment successful."
    - sh awardDeployBadge.sh
```

```yml
after_all:
  after_deploy:
    - do: echo "Application deployment successful."
      on:
        changed: /*/src/**
    - do: echo "Configuration deployment successful."
      on:
        changed:
          - /config/**
          - /*/config/**
```

----------

#### `after_all: after_failure`

Build step that runs after all project-level `after_success` and `after_failure` steps have finished.  This build step only runs if any project-level `script` step fails.  Once completed, the build jumps directly to `after_script`, and the build is considered failed.

__Possible values:__

* **_nil_**: take no action.

* __true__: disable this build step.

* __ShellCommand__: a shell command to execute.

* __ShellCommand[]__: an array of shell commands to execute.  All commands are executed in parallel.  If any command fails (exits with value > 0), all outstanding commands are allowed to complete.

* __Conditional[]__: an array of [Conditional](#conditional) objects.

__Default value:__ _nil_

__Examples:__

```yml
after_all:
  after_failure: echo "Build failed."
```

```yml
after_all:
  after_failure:
    - echo "Build failed."
    - sh awardBuildFailureBadge.sh
```

```yml
after_all:
  after_failure:
    - do: echo "Application build failed."
      on:
        changed: /*/src/**
    - do: echo "Configuration merge failed."
      on:
        changed:
          - /config/**
          - /*/config/**
```

----------

#### `after_all: after_script`

Build step that runs after all project-level `after_script` steps have finished.  This step will always run as long as the build progressed past `install`.  If `after_script` fails, then the build is considered failed.

__Possible values:__

* **_nil_**: take no action.

* __true__: disable this build step.

* __ShellCommand__: a shell command to execute.

* __ShellCommand[]__: an array of shell commands to execute.  All commands are executed in parallel.  If any command fails (exits with value > 0), all outstanding commands are allowed to complete.

* __Conditional[]__: an array of [Conditional](#conditional) objects.

__Default value:__ _nil_

__Examples:__

```yml
after_all:
  after_script: echo "Build complete."
```

```yml
after_all:
  after_script:
    - echo "Build complete."
    - sh awardBuildFailureBadge.sh
```

```yml
after_all:
  after_script:
    - do: echo "Application build completed with $CEMENT_CURRENT_STATUS."
      on:
        changed: /*/src/**
    - do: echo "Configuration merge complete with $CEMENT_CURRENT_STATUS."
      on:
        changed:
          - /config/**
          - /*/config/**
```

----------

#### `after_all: after_success`

Build step that runs after all project-level `after_success` and `after_failure` steps have finished.  This build step only runs if all project-level `script` steps succeeded.  If `after_success` fails, the build jumps to `after_script`, and the build is considered failed.

__Possible values:__

* **_nil_**: take no action.

* __true__: disable this build step.

* __ShellCommand__: a shell command to execute.

* __ShellCommand[]__: an array of shell commands to execute.  All commands are executed in parallel.  If any command fails (exits with value > 0), all outstanding commands are allowed to complete.

* __Conditional[]__: an array of [Conditional](#conditional) objects.

__Default value:__ _nil_

__Examples:__

```yml
after_all:
  after_success: echo "Build succeeded."
```

```yml
after_all:
  after_success:
    - echo "Build succeeded."
    - sh awardBuildSuccessBadge.sh
```

```yml
after_all:
  after_success:
    - do: echo "Application build succeeded."
      on:
        changed: /*/src/**
    - do: echo "Configuration merge succeeded."
      on:
        changed:
          - /config/**
          - /*/config/**
```

----------

#### `after_all: before_deploy`

Build step that runs after all project-level `before_deploy` steps have finished.  This build step only runs if all project-level `script` and `after_success` steps succeeded.  If `before_deploy` fails, the build jumps to `after_script`, and the build is considered failed.

__Possible values:__

* **_nil_**: take no action.

* __true__: disable this build step.

* __ShellCommand__: a shell command to execute.

* __ShellCommand[]__: an array of shell commands to execute.  All commands are executed in parallel.  If any command fails (exits with value > 0), all outstanding commands are allowed to complete.

* __Conditional[]__: an array of [Conditional](#conditional) objects.

__Default value:__ _nil_

__Examples:__

```yml
after_all:
  before_deploy: echo "Build succeeded."
```

```yml
after_all:
  before_deploy:
    - echo "Deploy initialization complete."
    - sh logDeployInitialization.sh
```

```yml
after_all:
  before_deploy:
    - do: echo "Source deploy initialization complete."
      on:
        changed: /*/src/**
    - do: echo "Configuration deploy initialization complete."
      on:
        changed:
          - /config/**
          - /*/config/**
```

----------

#### `after_all: before_install`

Build step that runs after all project-level `before_install` steps have finished.  If `before_install` fails, the build exits immediately as failed.

__Possible values:__

* **_nil_**: take no action.

* __true__: disable this build step.

* __ShellCommand__: a shell command to execute.

* __ShellCommand[]__: an array of shell commands to execute.  All commands are executed in parallel.  If any command fails (exits with value > 0), all outstanding commands are immediately halted.

* __Conditional[]__: an array of [Conditional](#conditional) objects.

__Default value:__ _nil_

__Examples:__

```yml
after_all:
  before_install: echo "Install initialization complete."
```

```yml
after_all:
  before_install:
    - echo "Install initialization complete."
    - sh prepareToInstall.sh
```

```yml
after_all:
  before_install:
    - do: echo "Install source build components complete."
      on:
        changed: /*/src/**
    - do: echo "Install configuration merge components complete."
      on:
        changed:
          - /config/**
          - /*/config/**
```

----------

#### `after_all: before_script`

Build step that runs after all project-level `before_script` steps have finished.  This build step only runs if all project-level `before_script` steps succeeded.  If `before_script` fails, the build jumps to `after_script`, and the build is considered failed.

__Possible values:__

* **_nil_**: take no action.

* __true__: disable this build step.

* __ShellCommand__: a shell command to execute.

* __ShellCommand[]__: an array of shell commands to execute.  All commands are executed in parallel.  If any command fails (exits with value > 0), all outstanding commands are allowed to complete.

* __Conditional[]__: an array of [Conditional](#conditional) objects.

__Default value:__ _nil_

__Examples:__

```yml
after_all:
  before_script: echo "Build initialization complete."
```

```yml
after_all:
  before_script:
    - echo "Build initialization complete."
    - sh prepareToBuild.sh
```

```yml
after_all:
  before_script:
    - do: echo "Build source initialization complete."
      on:
        changed: /*/src/**
    - do: echo "Merge configuration initialization complete."
      on:
        changed:
          - /config/**
          - /*/config/**
```

----------

#### `after_all: deploy`

Build step that runs after all project-level `deploy` steps have finished.  This step is only run if all project-level `deploy` steps completed successfully.  If `deploy` fails, the build jumps to `rollback`, and the build is considered failed.

__Possible values:__

* **_nil_**: take no action.

* __true__: disable this build step.

* __ShellCommand__: a shell command to execute.

* __ShellCommand[]__: an array of shell commands to execute.  All commands are executed in parallel.  If any command fails (exits with value > 0), all outstanding commands are allowed to complete.

* __Conditional[]__: an array of [Conditional](#conditional) objects.

__Default value:__ _nil_

__Examples:__

```yml
after_all:
  deploy: echo "Deploy completed."
```

```yml
after_all:
  deploy:
    - echo "Deploy completed."
    - sh finalizeDeploy.sh
```

```yml
after_all:
  deploy:
    - do: echo "Source deploy completed."
      on:
        changed: /*/src/**
    - do: echo "Configuration deploy completed."
      on:
        changed:
          - /config/**
          - /*/config/**
```

----------

#### `after_all: install`

Build step that runs after all project-level `install` steps have finished.  This step is only run if all project-level `install` steps completed successfully.  If `install` fails, the build exits immediately as failed.

__Possible values:__

* **_nil_**: take no action.

* __true__: disable this build step.

* __ShellCommand__: a shell command to execute.

* __ShellCommand[]__: an array of shell commands to execute.  All commands are executed in parallel.  If any command fails (exits with value > 0), all outstanding commands are immediately halted.

* __Conditional[]__: an array of [Conditional](#conditional) objects.

__Default value:__ _nil_

__Examples:__

```yml
after_all:
  install: echo "Install complete."
```

```yml
after_all:
  install:
    - echo "Install completed."
    - sh finalizeInstall.sh
```

```yml
after_all:
  install:
    - do: echo "Source component install complete."
      on:
        changed: /*/src/**
    - do: echo "Configuration component install complete."
      on:
        changed:
          - /config/**
          - /*/config/**
```

----------

#### `after_all: rollback`

Build step that runs after all project-level `rollback` steps have finished.  This step only runs if any `deploy` or `after_deploy` command fails.  After all `rollback` commands have completed, regardless if they were successful, the build jumps to `after_script`, and the build is considered failed.

__Possible values:__

* **_nil_**: take no action.

* __true__: disable this build step.

* __ShellCommand__: a shell command to execute.

* __ShellCommand[]__: an array of shell commands to execute.  All commands are executed in parallel.  If any command fails (exits with value > 0), all outstanding commands are allowed to complete.

* __Conditional[]__: an array of [Conditional](#conditional) objects.

__Default value:__ _nil_

__Examples:__

```yml
after_all:
  install: echo "Rollback complete."
```

```yml
after_all:
  install:
    - echo "Rollback completed."
    - sh notifyIfRollbackFailed.sh
```

```yml
after_all:
  install:
    - do: echo "Application rollback complete."
      on:
        changed: /*/src/**
    - do: echo "Configuration rollback complete."
      on:
        changed:
          - /config/**
          - /*/config/**
```

----------

#### `after_all: script`

Build step that runs after all project-level `script` steps have finished.  This build step only runs if all project-level `script` steps succeeded.  If `script` fails, the build jumps to `after_script`, and the build is considered failed.

__Possible values:__

* **_nil_**: take no action.

* __true__: disable this build step.

* __ShellCommand__: a shell command to execute.

* __ShellCommand[]__: an array of shell commands to execute.  All commands are executed in parallel.  If any command fails (exits with value > 0), all outstanding commands are allowed to complete.

* __Conditional[]__: an array of [Conditional](#conditional) objects.

__Default value:__ _nil_

__Examples:__

```yml
after_all:
  before_script: echo "Build script complete."
```

```yml
after_all:
  before_script:
    - echo "Build script complete."
    - sh awardBuildSuccessBadge.sh
```

```yml
after_all:
  before_script:
    - do: echo "Build source script complete."
      on:
        changed: /*/src/**
    - do: echo "Merge configuration script complete."
      on:
        changed:
          - /config/**
          - /*/config/**
```

----------

### `before_all`

Defines build stages to run after before their project-level counterparts.

----------

#### `before_all: after_deploy`

Build step that runs before all project-level `after_deploy` steps.  This build step only runs if all project-level and repository-level `deploy` steps succeeded.  If `after_deploy` fails, the build jumps to `rollback`, and the build is considered failed.

__Possible values:__

* **_nil_**: take no action.

* __true__: disable this build step.

* __ShellCommand__: a shell command to execute.

* __ShellCommand[]__: an array of shell commands to execute.  All commands are executed in parallel.  If any command fails (exits with value > 0), all outstanding commands are allowed to complete before rollback logic is executed.

* __Conditional[]__: an array of [Conditional](#conditional) objects.

__Default value:__ _nil_

__Examples:__

```yml
before_all:
  after_deploy: echo "Post-deployment beginning."
```

```yml
before_all:
  after_deploy:
    - echo "Post-deployment beginning."
    - sh initializePostDeploy.sh
```

```yml
before_all:
  after_deploy:
    - do: echo "Post-application deployment beginning."
      on:
        changed: /*/src/**
    - do: echo "Post-configuration deployment beginning."
      on:
        changed:
          - /config/**
          - /*/config/**
```

----------

#### `before_all: after_failure`

Build step that runs before all project-level `after_success` and `after_failure` steps.  This build step only runs if any project-level `script` step fails.

__Possible values:__

* **_nil_**: take no action.

* __true__: disable this build step.

* __ShellCommand__: a shell command to execute.

* __ShellCommand[]__: an array of shell commands to execute.  All commands are executed in parallel.  If any command fails (exits with value > 0), all outstanding commands are allowed to complete.

* __Conditional[]__: an array of [Conditional](#conditional) objects.

__Default value:__ _nil_

__Examples:__

```yml
before_all:
  after_failure: echo "Build failed."
```

```yml
before_all:
  after_failure:
    - echo "Beginning script failure handlers."
    - sh initializeScriptFailure.sh
```

```yml
before_all:
  after_failure:
    - do: echo "Beginning source script failure handlers."
      on:
        changed: /*/src/**
    - do: echo "Beginning configuration script failure handlers."
      on:
        changed:
          - /config/**
          - /*/config/**
```

----------

#### `before_all: after_script`

Build step that runs before all project-level `after_script` steps.  This step will always run as long as the build progressed past `install`.  If `after_script` fails, then the build is considered failed.

__Possible values:__

* **_nil_**: take no action.

* __true__: disable this build step.

* __ShellCommand__: a shell command to execute.

* __ShellCommand[]__: an array of shell commands to execute.  All commands are executed in parallel.  If any command fails (exits with value > 0), all outstanding commands are allowed to complete.

* __Conditional[]__: an array of [Conditional](#conditional) objects.

__Default value:__ _nil_

__Examples:__

```yml
before_all:
  after_script: echo "Finalizing build."
```

```yml
before_all:
  after_script:
    - echo "Finalizing build."
    - sh prepareToFinalize.sh
```

```yml
before_all:
  after_script:
    - do: echo "Finalizing source build."
      on:
        changed: /*/src/**
    - do: echo "Finalizing configuration merge."
      on:
        changed:
          - /config/**
          - /*/config/**
```

----------

#### `before_all: after_success`

Build step that runs before all project-level `after_success` and `after_failure` steps.  This build step only runs if all project-level `script` steps succeeded.  If `after_success` fails, the build jumps to `after_script`, and the build is considered failed.

__Possible values:__

* **_nil_**: take no action.

* __true__: disable this build step.

* __ShellCommand__: a shell command to execute.

* __ShellCommand[]__: an array of shell commands to execute.  All commands are executed in parallel.  If any command fails (exits with value > 0), all outstanding commands are allowed to complete.

* __Conditional[]__: an array of [Conditional](#conditional) objects.

__Default value:__ _nil_

__Examples:__

```yml
before_all:
  after_success: echo "Build succeeded.  Beginning post-build."
```

```yml
before_all:
  after_success:
    - echo "Build succeeded.  Beginning post-build."
    - sh initializePostBuild.sh
```

```yml
before_all:
  after_success:
    - do: echo "Application build succeeded.  Beginning post-build."
      on:
        changed: /*/src/**
    - do: echo "Configuration merge succeeded.  Beginning post-merge."
      on:
        changed:
          - /config/**
          - /*/config/**
```

----------

#### `before_all: before_deploy`

Build step that runs before all project-level `before_deploy` steps.  This build step only runs if all project-level `script` and `after_success` steps succeeded.  If `before_deploy` fails, the build jumps to `after_script`, and the build is considered failed.

__Possible values:__

* **_nil_**: take no action.

* __true__: disable this build step.

* __ShellCommand__: a shell command to execute.

* __ShellCommand[]__: an array of shell commands to execute.  All commands are executed in parallel.  If any command fails (exits with value > 0), all outstanding commands are allowed to complete.

* __Conditional[]__: an array of [Conditional](#conditional) objects.

__Default value:__ _nil_

__Examples:__

```yml
before_all:
  before_deploy: echo "Beginning pre-deploy."
```

```yml
before_all:
  before_deploy:
    - echo "Beginning pre-deploy."
    - sh initializePreDeploy.sh
```

```yml
before_all:
  before_deploy:
    - do: echo "Beginning source deploy initialization."
      on:
        changed: /*/src/**
    - do: echo "Beginning configuration deploy initialization."
      on:
        changed:
          - /config/**
          - /*/config/**
```

----------

#### `before_all: before_install`

Build step that runs before all project-level `before_install` steps.  If `before_install` fails, the build exits immediately as failed.

__Possible values:__

* **_nil_**: take no action.

* __true__: disable this build step.

* __ShellCommand__: a shell command to execute.

* __ShellCommand[]__: an array of shell commands to execute.  All commands are executed in parallel.  If any command fails (exits with value > 0), all outstanding commands are immediately halted.

* __Conditional[]__: an array of [Conditional](#conditional) objects.

__Default value:__ _nil_

__Examples:__

```yml
before_all:
  before_install: echo "Beginning install initialization."
```

```yml
before_all:
  before_install:
    - echo "Beginning install initialization."
    - sh prepareToInstall.sh
```

```yml
before_all:
  before_install:
    - do: echo "Beginning install source build components."
      on:
        changed: /*/src/**
    - do: echo "Beginning install configuration merge components."
      on:
        changed:
          - /config/**
          - /*/config/**
```

----------

#### `before_all: before_script`

Build step that runs before all project-level `before_script` steps.  This build step only runs if all project-level `before_script` steps succeeded.  If `before_script` fails, the build jumps to `after_script`, and the build is considered failed.

__Possible values:__

* **_nil_**: take no action.

* __true__: disable this build step.

* __ShellCommand__: a shell command to execute.

* __ShellCommand[]__: an array of shell commands to execute.  All commands are executed in parallel.  If any command fails (exits with value > 0), all outstanding commands are allowed to complete.

* __Conditional[]__: an array of [Conditional](#conditional) objects.

__Default value:__ _nil_

__Examples:__

```yml
before_all:
  before_script: echo "Beginning build initialization."
```

```yml
before_all:
  before_script:
    - echo "Beginning build initialization."
    - sh prepareToBuild.sh
```

```yml
before_all:
  before_script:
    - do: echo "Beginning build source initialization."
      on:
        changed: /*/src/**
    - do: echo "Beginning merge configuration initialization."
      on:
        changed:
          - /config/**
          - /*/config/**
```

----------

#### `before_all: deploy`

Build step that runs before all project-level `deploy` steps.  This step is only run if all project-level `deploy` steps completed successfully.  If `deploy` fails, the build jumps to `rollback`, and the build is considered failed.

__Possible values:__

* **_nil_**: take no action.

* __true__: disable this build step.

* __ShellCommand__: a shell command to execute.

* __ShellCommand[]__: an array of shell commands to execute.  All commands are executed in parallel.  If any command fails (exits with value > 0), all outstanding commands are allowed to complete.

* __Conditional[]__: an array of [Conditional](#conditional) objects.

__Default value:__ _nil_

__Examples:__

```yml
before_all:
  deploy: echo "Beginning deploy."
```

```yml
before_all:
  deploy:
    - echo "Beginning deploy."
    - sh prepareDeploy.sh
```

```yml
before_all:
  deploy:
    - do: echo "Beginning source deploy."
      on:
        changed: /*/src/**
    - do: echo "Beginning configuration deploy."
      on:
        changed:
          - /config/**
          - /*/config/**
```

----------

#### `before_all: install`

Build step that runs before all project-level `install` steps.  This step is only run if all project-level `install` steps completed successfully.  If `install` fails, the build exits immediately as failed.

__Possible values:__

* **_nil_**: take no action.

* __true__: disable this build step.

* __ShellCommand__: a shell command to execute.

* __ShellCommand[]__: an array of shell commands to execute.  All commands are executed in parallel.  If any command fails (exits with value > 0), all outstanding commands are immediately halted.

* __Conditional[]__: an array of [Conditional](#conditional) objects.

__Default value:__ _nil_

__Examples:__

```yml
before_all:
  install: echo "Beginning install."
```

```yml
before_all:
  install:
    - echo "Beginning install."
    - sh initializeInstall.sh
```

```yml
before_all:
  install:
    - do: echo "Beginning source component install."
      on:
        changed: /*/src/**
    - do: echo "Beginning configuration component install."
      on:
        changed:
          - /config/**
          - /*/config/**
```

----------

#### `before_all: rollback`

Build step that runs before all project-level `rollback` steps.  This step only runs if any `deploy` or `after_deploy` command fails.  After all `rollback` commands have completed, regardless if they were successful, the build jumps to `after_script`, and the build is considered failed.

__Possible values:__

* **_nil_**: take no action.

* __true__: disable this build step.

* __ShellCommand__: a shell command to execute.

* __ShellCommand[]__: an array of shell commands to execute.  All commands are executed in parallel.  If any command fails (exits with value > 0), all outstanding commands are allowed to complete.

* __Conditional[]__: an array of [Conditional](#conditional) objects.

__Default value:__ _nil_

__Examples:__

```yml
before_all:
  rollback: echo "Beginning rollback."
```

```yml
before_all:
  rollback:
    - echo "Beginning rollback."
    - sh initializeRollback.sh
```

```yml
before_all:
  rollback:
    - do: echo "Beginning application rollback."
      on:
        changed: /*/src/**
    - do: echo "Beginning configuration rollback."
      on:
        changed:
          - /config/**
          - /*/config/**
```

----------

#### `before_all: script`

Build step that runs before all project-level `script` steps.  This build step only runs if all project-level `script` steps succeeded.  If `script` fails, the build jumps to `after_script`, and the build is considered failed.

__Possible values:__

* **_nil_**: take no action.

* __true__: disable this build step.

* __ShellCommand__: a shell command to execute.

* __ShellCommand[]__: an array of shell commands to execute.  All commands are executed in parallel.  If any command fails (exits with value > 0), all outstanding commands are allowed to complete.

* __Conditional[]__: an array of [Conditional](#conditional) objects.

__Default value:__ _nil_

__Examples:__

```yml
before_all:
  script: echo "Beginning build script."
```

```yml
before_all:
  script:
    - echo "Beginning build script."
    - sh initializeBuild.sh
```

```yml
before_all:
  script:
    - do: echo "Beginning source build script."
      on:
        changed: /*/src/**
    - do: echo "Beginning configuration merge script."
      on:
        changed:
          - /config/**
          - /*/config/**
```

----------

### `for_each`

Defines the build steps to run on each modified project.

---------

#### `for_each: after_deploy`

Build step that runs for each modified project in the monorepo after `deploy` has completed.  This build step only runs if all project-level and repository-level `deploy` steps succeeded.  If `after_deploy` fails, the build jumps to `rollback`, and the build is considered failed.

__Possible values:__

* **_nil_**: take no action.

* __true__: disable this build step.

* __ShellCommand__: a shell command to execute.

* __ShellCommand[]__: an array of shell commands to execute.  All commands are executed in parallel.  If any command fails (exits with value > 0), all outstanding commands are allowed to complete before rollback logic is executed.

* __Conditional[]__: an array of [Conditional](#conditional) objects.

__Default value:__ _nil_

__Examples:__

```yml
for_each:
  after_deploy: sh validateDeploy.sh $CEMENT_CURRENT_PROJECT_NAME
```

```yml
for_each:
  after_deploy:
    - echo "Project $CEMENT_CURRENT_PROJECT_NAME deployed"
    - sh validateDeploy.sh $CEMENT_CURRENT_PROJECT_NAME
```

```yml
for_each:
  after_deploy:
    - do: sh validateDeploy.sh $CEMENT_CURRENT_PROJECT_NAME
      on:
        changed: /src/**
    - do: sh validateConfigDeploy.sh $CEMENT_CURRENT_PROJECT_NAME
      on:
        changed: /config/**
```

----------

#### `for_each: after_failure`

Build step that runs for each modified project in the monorepo with a `script` failure.  This build step only runs if a project-level `script` step fails.

__Possible values:__

* **_nil_**: take no action.

* __true__: disable this build step.

* __ShellCommand__: a shell command to execute.

* __ShellCommand[]__: an array of shell commands to execute.  All commands are executed in parallel.  If any command fails (exits with value > 0), all outstanding commands are allowed to complete.

* __Conditional[]__: an array of [Conditional](#conditional) objects.

__Default value:__ _nil_

__Examples:__

```yml
for_each:
  after_failure: echo "Build failed for $CEMENT_CURRENT_PROJECT_NAME"
```

```yml
for_each:
  after_failure:
    - echo "Build failed for $CEMENT_CURRENT_PROJECT_NAME"
    - sh cleanup.sh $CEMENT_CURRENT_PROJECT_NAME
```

```yml
for_each:
  after_failure:
    - do: echo "Source build failed for $CEMENT_CURRENT_PROJECT_NAME"
      on:
        changed: /src/**
    - do: echo "Configuration merge failed for $CEMENT_CURRENT_PROJECT_NAME"
      on:
        changed: /config/**
```

----------

#### `for_each: after_script`

Build step that runs for each modified project in the monorepo at the end of every build.  This step will always run as long as the build progressed past `install`.  If `after_script` fails, then the build is considered failed.

__Possible values:__

* **_nil_**: take no action.

* __true__: disable this build step.

* __ShellCommand__: a shell command to execute.

* __ShellCommand[]__: an array of shell commands to execute.  All commands are executed in parallel.  If any command fails (exits with value > 0), all outstanding commands are allowed to complete.

* __Conditional[]__: an array of [Conditional](#conditional) objects.

__Default value:__ _nil_

__Examples:__

```yml
for_each:
  after_script: echo "Finalizing $CEMENT_CURRENT_PROJECT_NAME"
```

```yml
for_each:
  after_script:
    - echo "Finalizing build $CEMENT_CURRENT_PROJECT_NAME"
    - sh finalize.sh $CEMENT_CURRENT_PROJECT_NAME
```

```yml
for_each:
  after_script:
    - do: echo "Finalizing source build for $CEMENT_CURRENT_PROJECT_NAME."
      on:
        changed: /src/**
    - do: echo "Finalizing configuration merge for $CEMENT_CURRENT_PROJECT_NAME"
      on:
        changed: /config/**
```

----------

#### `for_each: after_success`

Build step that runs for each modified project in the monorepo after a successful `script` step.  This build step only runs if the project's `script` step succeeded.  If `after_success` fails, `deploy` will be skipped, and the build will be considered failed.

__Possible values:__

* **_nil_**: take no action.

* __true__: disable this build step.

* __ShellCommand__: a shell command to execute.

* __ShellCommand[]__: an array of shell commands to execute.  All commands are executed in parallel.  If any command fails (exits with value > 0), all outstanding commands are allowed to complete.

* __Conditional[]__: an array of [Conditional](#conditional) objects.

__Default value:__ _nil_

__Examples:__

```yml
for_each:
  after_success: echo "Build succeeded for $CEMENT_CURRENT_PROJECT_NAME"
```

```yml
for_each:
  after_success:
    - echo "Build succeeded for $CEMENT_CURRENT_PROJECT_NAME"
    - sh cleanup.sh $CEMENT_CURRENT_PROJECT_NAME
```

```yml
for_each:
  after_success:
    - do: echo "Source build succeeded for $CEMENT_CURRENT_PROJECT_NAME"
      on:
        changed: /src/**
    - do: echo "Configuration merge succeeded for $CEMENT_CURRENT_PROJECT_NAME"
      on:
        changed: /config/**
```

----------

#### `for_each: before_deploy`

Build step that runs for each modified project in the monorepo before the `deploy` step is run.  This build step only runs if all project-level `script` and `after_success` steps succeeded.  If `before_deploy` fails, the build is considered failed.

__Possible values:__

* **_nil_**: take no action.

* __true__: disable this build step.

* __ShellCommand__: a shell command to execute.

* __ShellCommand[]__: an array of shell commands to execute.  All commands are executed in parallel.  If any command fails (exits with value > 0), all outstanding commands are allowed to complete.

* __Conditional[]__: an array of [Conditional](#conditional) objects.

__Default value:__ _nil_

__Examples:__

```yml
for_each:
  before_deploy: echo "Beginning deploy for $CEMENT_CURRENT_PROJECT_NAME"
```

```yml
for_each:
  before_deploy:
    - echo "Beginning pre-deploy."
    - sh prepareDeploy.sh $CEMENT_CURRENT_PROJECT_NAME
```

```yml
for_each:
  before_deploy:
    - do: echo "Beginning source deploy for $CEMENT_CURRENT_PROJECT_NAME"
      on:
        changed: /src/**
    - do: echo "Beginning configuration deploy for $CEMENT_CURRENT_PROJECT_NAME"
      on:
        changed: /config/**
```

----------

#### `for_each: before_install`

Build step that runs for each modified project in the monorepo before the `install` step is run.  If `before_install` fails, the build exits immediately as failed.

__Possible values:__

* **_nil_**: take no action.

* __true__: disable this build step.

* __ShellCommand__: a shell command to execute.

* __ShellCommand[]__: an array of shell commands to execute.  All commands are executed in parallel.  If any command fails (exits with value > 0), all outstanding commands are immediately halted.

* __Conditional[]__: an array of [Conditional](#conditional) objects.

__Default value:__ _nil_

__Examples:__

```yml
for_each:
  before_install: echo "Beginning install initialization for $CEMENT_CURRENT_PROJECT_NAME"
```

```yml
for_each:
  before_install:
    - echo "Beginning install initialization $CEMENT_CURRENT_PROJECT_NAME"
    - sh prepareToInstall.sh $CEMENT_CURRENT_PROJECT_NAME
```

```yml
for_each:
  before_install:
    - do: echo "Beginning install source build components for $CEMENT_CURRENT_PROJECT_NAME"
      on:
        changed: /src/**
    - do: echo "Beginning install configuration merge components for $CEMENT_CURRENT_PROJECT_NAME"
      on:
        changed: /config/**
```

----------

#### `for_each: before_script`

Build step that runs for each modified project in the monorepo before the `script` step is run.  This build step only runs if all installation-related steps succeeded.  If `before_script` fails, `script` will be skipped, and the build will be considered failed.

__Possible values:__

* **_nil_**: take no action.

* __true__: disable this build step.

* __ShellCommand__: a shell command to execute.

* __ShellCommand[]__: an array of shell commands to execute.  All commands are executed in parallel.  If any command fails (exits with value > 0), all outstanding commands are allowed to complete.

* __Conditional[]__: an array of [Conditional](#conditional) objects.

__Default value:__ _nil_

__Examples:__

```yml
for_each:
  before_script: echo "Beginning build initialization for $CEMENT_CURRENT_PROJECT_NAME"
```

```yml
for_each:
  before_script:
    - echo "Beginning build initialization for $CEMENT_CURRENT_PROJECT_NAME"
    - sh prepareToBuild.sh $CEMENT_CURRENT_PROJECT_NAME
```

```yml
for_each:
  before_script:
    - do: echo "Beginning build source initialization for $CEMENT_CURRENT_PROJECT_NAME"
      on:
        changed: /src/**
    - do: echo "Beginning merge configuration initialization for $CEMENT_CURRENT_PROJECT_NAME"
      on:
        changed: /config/**
```

----------

#### `for_each: deploy`

Build step that runs for each modified project in the monorepo intended to provide build logic to Cement and TravisCI.  This step is only run if all build logic provided by the various `script` steps completed successfully.  If `deploy` fails, the build jumps to `rollback`, and the build is considered failed.

__Possible values:__

* **_nil_**: take no action.

* __true__: disable this build step.

* __ShellCommand__: a shell command to execute.

* __ShellCommand[]__: an array of shell commands to execute.  All commands are executed in parallel.  If any command fails (exits with value > 0), all outstanding commands are allowed to complete.

* __Deployment__: a [Deployment](#deployment) object.

* __(Deployment | Conditional)[]__: an array of Deployment or [Conditional](#conditional) objects.

__Default value:__ _nil_

__Examples:__

```yml
for_each:
  deploy: sh deploy.sh $CEMENT_CURRENT_PROJECT_NAME
```

```yml
for_each:
  deploy:
    - echo "Deploying $CEMENT_CURRENT_PROJECT_NAME"
    - sh deploy.sh $CEMENT_CURRENT_PROJECT_NAME
```

```yml
for_each:
  deploy:
    - do: sh deploy.sh $CEMENT_CURRENT_PROJECT_NAME
      on:
        changed: /src/**
    - do: sh deploy_config.sh $CEMENT_CURRENT_PROJECT_NAME
      on:
        changed: /config/**
```

```yml
for_each:
  deploy:
    provider: heroku
    api_key: ...
    app: $CEMENT_CURRENT_PROJECT_NAME
```

```yml
for_each:
  deploy:
    - provider: heroku
      api_key: ...
      app: $CEMENT_CURRENT_PROJECT_NAME
      on:
        changed: /src/**
    - do: CONFIG_VARS=$(cat prod.env) && heroku config:set $CONFIG_VARS
      on:
        changed: /config/**
```

----------

#### `for_each: install`

The `install` step run for each modified project in the monorepo.  This step is only run if all `before_install` steps completed successfully.  If `install` fails, the build exits immediately as failed.

__Possible values:__

* **_nil_**: take no action.

* __true__: disable this build step.

* __ShellCommand__: a shell command to execute.

* __ShellCommand[]__: an array of shell commands to execute.  All commands are executed in parallel.  If any command fails (exits with value > 0), all outstanding commands are immediately halted.

* __Conditional[]__: an array of [Conditional](#conditional) objects.

__Default value:__ _nil_

__Examples:__

```yml
for_each:
  install: make install
```

```yml
for_each:
  install:
    - echo "Installing dependencies for $CEMENT_CURRENT_PROJECT_NAME"
    - make install
```

```yml
for_each:
  install:
    - do: make install:src
      on:
        changed: /src/**
    - do: make install:config
      on:
        changed: /config/**
```

----------

#### `for_each: rollback`

The `rollback` step run for each modified projects in the monorepo.  This step only runs if any `deploy` or `after_deploy` command fails.  After all `rollback` commands have completed, regardless if they were successful, the build jumps to `after_script`, and the build is considered failed.

__Possible values:__

* **_nil_**: take no action.

* __true__: disable this build step.

* __ShellCommand__: a shell command to execute.

* __ShellCommand[]__: an array of shell commands to execute.  All commands are executed in parallel.  If any command fails (exits with value > 0), all outstanding commands are allowed to complete.

* __Conditional[]__: an array of [Conditional](#conditional) objects.

__Default value:__ _nil_

__Examples:__

```yml
for_each:
  rollback: sh rollback.sh
```

```yml
for_each:
  rollback:
    - echo "Rolling back $CEMENT_CURRENT_PROJECT_NAME"
    - sh rollback.sh
```

```yml
for_each:
  rollback:
    - do: sh rollback.sh
      on:
        changed: /src/**
    - do: sh rollback_config.sh
      on:
        changed: /config/**
```

----------

#### `for_each: script`

The build `script` step run for each modified project in the monorepo.  This build step only runs if all `install` related steps succeeded.  If `script` fails, the build jumps to `after_script`, and the build is considered failed.

__Possible values:__

* **_nil_**: take no action.

* __true__: disable this build step.

* __ShellCommand__: a shell command to execute.

* __ShellCommand[]__: an array of shell commands to execute.  All commands are executed in parallel.  If any command fails (exits with value > 0), all outstanding commands are allowed to complete.

* __Conditional[]__: an array of [Conditional](#conditional) objects.

__Default value:__ _nil_

__Examples:__

```yml
for_each:
  script: make build
```

```yml
for_each:
  script:
    - echo "Building $CEMENT_CURRENT_PROJECT_NAME"
    - make build
```

```yml
for_each:
  script:
    - do: make build
      on:
        changed: /src/**
    - do: make merge_config
      on:
        changed: /config/**
```

----------

### `ignore`

Defines one or more globs used to identify directories and files Cement should ignore.  This property is used when Cement is figuring which projects on which to execute build and deploy logic.  All globs are compared against the full paths of all modified files in the Git commit that triggered the build.  Matches are ignored.

__Possible values:__

* **_nil_**: nothing is ignored.

+ __glob__: a path matching pattern.

* __glob[]__: an array of path matching patterns evaluated in order.

__Default value:__ _nil_

__Example:__

```yml
ignore:
  - /docs/**
  - /*/docs/**
```

----------

### `projects_root`

A directory relative to the `.cement.yml` file that contains all project directories.  All 

__Possible values:__

* **_nil_**: use the default value.

* __/path/to/directory__: a path to the directory containing project directories.

__Default value:__ `/`

__Example:__

```yml
project_root: /projects
```

----------

## Override Configuration

Override configuration is stored in a `.cementover.yml` file, and located within the root of a project directory.  This file is optional.

----------

### `after_deploy`

Overrides the `for_each: after_deploy` step defined in the root `.cement.yml` file for the project containing the `.cementover.yml` file.

__Possible values:__

* **_nil_**: take no action.

* __true__: disable this build step.

* __ShellCommand__: a shell command to execute.

* __ShellCommand[]__: an array of shell commands to execute.  All commands are executed in parallel.  If any command fails (exits with value > 0), all outstanding commands are allowed to complete before rollback logic is executed.

* __Conditional[]__: an array of [Conditional](#conditional) objects.

__Default value:__ _nil_

__Examples:__

```yml
after_deploy: sh validateDeploy.sh $CEMENT_CURRENT_PROJECT_NAME
```

```yml
after_deploy:
  - echo "Project $CEMENT_CURRENT_PROJECT_NAME deployed"
  - sh validateDeploy.sh $CEMENT_CURRENT_PROJECT_NAME
```

```yml
after_deploy:
  - do: sh validateDeploy.sh $CEMENT_CURRENT_PROJECT_NAME
    on:
      changed: /src/**
  - do: sh validateConfigDeploy.sh $CEMENT_CURRENT_PROJECT_NAME
    on:
      changed: /config/**
```

----------

### `after_failure`

Overrides the `for_each: after_failure` step defined in the root `.cement.yml` file for the project containing the `.cementover.yml` file.

__Possible values:__

* **_nil_**: take no action.

* __true__: disable this build step.

* __ShellCommand__: a shell command to execute.

* __ShellCommand[]__: an array of shell commands to execute.  All commands are executed in parallel.  If any command fails (exits with value > 0), all outstanding commands are allowed to complete.

* __Conditional[]__: an array of [Conditional](#conditional) objects.

__Default value:__ _nil_

__Examples:__

```yml
after_failure: echo "Build failed for $CEMENT_CURRENT_PROJECT_NAME"
```

```yml
after_failure:
  - echo "Build failed for $CEMENT_CURRENT_PROJECT_NAME"
  - sh cleanup.sh $CEMENT_CURRENT_PROJECT_NAME
```

```yml
after_failure:
  - do: echo "Source build failed for $CEMENT_CURRENT_PROJECT_NAME"
    on:
      changed: /src/**
  - do: echo "Configuration merge failed for $CEMENT_CURRENT_PROJECT_NAME"
    on:
      changed: /config/**
```

----------

### `after_script`

Overrides the `for_each: after_script` step defined in the root `.cement.yml` file for the project containing the `.cementover.yml` file.

__Possible values:__

* **_nil_**: take no action.

* __true__: disable this build step.

* __ShellCommand__: a shell command to execute.

* __ShellCommand[]__: an array of shell commands to execute.  All commands are executed in parallel.  If any command fails (exits with value > 0), all outstanding commands are allowed to complete.

* __Conditional[]__: an array of [Conditional](#conditional) objects.

__Default value:__ _nil_

__Examples:__

```yml
after_script: echo "Finalizing $CEMENT_CURRENT_PROJECT_NAME"
```

```yml
after_script:
  - echo "Finalizing build $CEMENT_CURRENT_PROJECT_NAME"
  - sh finalize.sh $CEMENT_CURRENT_PROJECT_NAME
```

```yml
after_script:
  - do: echo "Finalizing source build for $CEMENT_CURRENT_PROJECT_NAME."
    on:
      changed: /src/**
  - do: echo "Finalizing configuration merge for $CEMENT_CURRENT_PROJECT_NAME"
    on:
      changed: /config/**
```

----------

### `after_success`

Overrides the `for_each: after_success` step defined in the root `.cement.yml` file for the project containing the `.cementover.yml` file.

__Possible values:__

* **_nil_**: take no action.

* __true__: disable this build step.

* __ShellCommand__: a shell command to execute.

* __ShellCommand[]__: an array of shell commands to execute.  All commands are executed in parallel.  If any command fails (exits with value > 0), all outstanding commands are allowed to complete.

* __Conditional[]__: an array of [Conditional](#conditional) objects.

__Default value:__ _nil_

__Examples:__

```yml
after_success: echo "Build succeeded for $CEMENT_CURRENT_PROJECT_NAME"
```

```yml
after_success:
  - echo "Build succeeded for $CEMENT_CURRENT_PROJECT_NAME"
  - sh cleanup.sh $CEMENT_CURRENT_PROJECT_NAME
```

```yml
after_success:
  - do: echo "Source build succeeded for $CEMENT_CURRENT_PROJECT_NAME"
    on:
      changed: /src/**
  - do: echo "Configuration merge succeeded for $CEMENT_CURRENT_PROJECT_NAME"
    on:
      changed: /config/**
```

----------

### `before_deploy`

Overrides the `for_each: before_deploy` step defined in the root `.cement.yml` file for the project containing the `.cementover.yml` file.

__Possible values:__

* **_nil_**: take no action.

* __true__: disable this build step.

* __ShellCommand__: a shell command to execute.

* __ShellCommand[]__: an array of shell commands to execute.  All commands are executed in parallel.  If any command fails (exits with value > 0), all outstanding commands are allowed to complete.

* __Conditional[]__: an array of [Conditional](#conditional) objects.

__Default value:__ _nil_

__Examples:__

```yml
before_deploy: echo "Beginning deploy for $CEMENT_CURRENT_PROJECT_NAME"
```

```yml
before_deploy:
  - echo "Beginning pre-deploy."
  - sh prepareDeploy.sh $CEMENT_CURRENT_PROJECT_NAME
```

```yml
before_deploy:
  - do: echo "Beginning source deploy for $CEMENT_CURRENT_PROJECT_NAME"
    on:
      changed: /src/**
  - do: echo "Beginning configuration deploy for $CEMENT_CURRENT_PROJECT_NAME"
    on:
      changed: /config/**
```

----------

### `before_install`

Overrides the `for_each: before_install` step defined in the root `.cement.yml` file for the project containing the `.cementover.yml` file.

__Possible values:__

* **_nil_**: take no action.

* __true__: disable this build step.

* __ShellCommand__: a shell command to execute.

* __ShellCommand[]__: an array of shell commands to execute.  All commands are executed in parallel.  If any command fails (exits with value > 0), all outstanding commands are immediately halted.

* __Conditional[]__: an array of [Conditional](#conditional) objects.

__Default value:__ _nil_

__Examples:__

```yml
before_install: echo "Beginning install initialization for $CEMENT_CURRENT_PROJECT_NAME"
```

```yml
before_install:
  - echo "Beginning install initialization $CEMENT_CURRENT_PROJECT_NAME"
  - sh prepareToInstall.sh $CEMENT_CURRENT_PROJECT_NAME
```

```yml
before_install:
  - do: echo "Beginning install source build components for $CEMENT_CURRENT_PROJECT_NAME"
    on:
      changed: /src/**
  - do: echo "Beginning install configuration merge components for $CEMENT_CURRENT_PROJECT_NAME"
    on:
      changed: /config/**
```

----------

### `before_script`

Overrides the `for_each: before_script` step defined in the root `.cement.yml` file for the project containing the `.cementover.yml` file.

__Possible values:__

* **_nil_**: take no action.

* __true__: disable this build step.

* __ShellCommand__: a shell command to execute.

* __ShellCommand[]__: an array of shell commands to execute.  All commands are executed in parallel.  If any command fails (exits with value > 0), all outstanding commands are allowed to complete.

* __Conditional[]__: an array of [Conditional](#conditional) objects.

__Default value:__ _nil_

__Examples:__

```yml
before_script: echo "Beginning build initialization for $CEMENT_CURRENT_PROJECT_NAME"
```

```yml
before_script:
  - echo "Beginning build initialization for $CEMENT_CURRENT_PROJECT_NAME"
  - sh prepareToBuild.sh $CEMENT_CURRENT_PROJECT_NAME
```

```yml
before_script:
  - do: echo "Beginning build source initialization for $CEMENT_CURRENT_PROJECT_NAME"
    on:
      changed: /src/**
  - do: echo "Beginning merge configuration initialization for $CEMENT_CURRENT_PROJECT_NAME"
    on:
      changed: /config/**
```

----------

### `deploy`

Overrides the `for_each: deploy` step defined in the root `.cement.yml` file for the project containing the `.cementover.yml` file.

__Possible values:__

* **_nil_**: take no action.

* __true__: disable this build step.

* __ShellCommand__: a shell command to execute.

* __ShellCommand[]__: an array of shell commands to execute.  All commands are executed in parallel.  If any command fails (exits with value > 0), all outstanding commands are allowed to complete.

* __Deployment__: a [Deployment](#deployment) object.

* __(Deployment | Conditional)[]__: an array of Deployment or [Conditional](#conditional) objects.

__Default value:__ _nil_

__Examples:__

```yml
deploy: sh deploy.sh $CEMENT_CURRENT_PROJECT_NAME
```

```yml
deploy:
  - echo "Deploying $CEMENT_CURRENT_PROJECT_NAME"
  - sh deploy.sh $CEMENT_CURRENT_PROJECT_NAME
```

```yml
deploy:
  - do: sh deploy.sh $CEMENT_CURRENT_PROJECT_NAME
    on:
      changed: /src/**
  - do: sh deploy_config.sh $CEMENT_CURRENT_PROJECT_NAME
    on:
      changed: /config/**
```

```yml
deploy:
  provider: heroku
  api_key: ...
  app: $CEMENT_CURRENT_PROJECT_NAME
```

```yml
deploy:
  - provider: heroku
    api_key: ...
    app: $CEMENT_CURRENT_PROJECT_NAME
    on:
      changed: /src/**
  - do: CONFIG_VARS=$(cat prod.env) && heroku config:set $CONFIG_VARS
    on:
      changed: /config/**
```

----------

### `ignore`

Defines one or more globs used to identify directories and files Cement should ignore.  This property is used when Cement is figuring which projects on which to execute build and deploy logic.  All globs are compared against the full paths of all modified files in the Git commit that triggered the build.  Matches are ignored.

__Possible values:__

* **_nil_**: nothing is ignored.

+ __glob__: a path matching pattern.

* __glob[]__: an array of path matching patterns evaluated in order.

__Default value:__ _nil_

__Example:__

```yml
ignore:
  - /docs/**
  - /*/docs/**
```

----------

### `install`

Overrides the `for_each: install` step defined in the root `.cement.yml` file for the project containing the `.cementover.yml` file.

__Possible values:__

* **_nil_**: take no action.

* __true__: disable this build step.

* __ShellCommand__: a shell command to execute.

* __ShellCommand[]__: an array of shell commands to execute.  All commands are executed in parallel.  If any command fails (exits with value > 0), all outstanding commands are immediately halted.

* __Conditional[]__: an array of [Conditional](#conditional) objects.

__Default value:__ _nil_

__Examples:__

```yml
install: make install
```

```yml
install:
  - echo "Installing dependencies for $CEMENT_CURRENT_PROJECT_NAME"
  - make install
```

```yml
install:
  - do: make install:src
    on:
      changed: /src/**
  - do: make install:config
    on:
      changed: /config/**
```

----------

### `override`

Dictates how Cement merges overrides with base configuration.

__Possible values:__

* **_nil_**: use the default.

* __`merge`__: all build step configuration in override file is merged with the base.

* __`replace`__: all build step configuration in override file completely overwrites the base.

__Default value:__ `merge`

__Example:__

```yml
override: replace
```

----------

### `rollback`

Overrides the `for_each: rollback` step defined in the root `.cement.yml` file for the project containing the `.cementover.yml` file.

__Possible values:__

* **_nil_**: take no action.

* __true__: disable this build step.

* __ShellCommand__: a shell command to execute.

* __ShellCommand[]__: an array of shell commands to execute.  All commands are executed in parallel.  If any command fails (exits with value > 0), all outstanding commands are allowed to complete.

* __Conditional[]__: an array of [Conditional](#conditional) objects.

__Default value:__ _nil_

__Examples:__

```yml
rollback: sh rollback.sh
```

```yml
rollback:
  - echo "Rolling back $CEMENT_CURRENT_PROJECT_NAME"
  - sh rollback.sh
```

```yml
rollback:
  - do: sh rollback.sh
    on:
      changed: /src/**
  - do: sh rollback_config.sh
    on:
      changed: /config/**
```

----------

### `script`

Overrides the `for_each: script` step defined in the root `.cement.yml` file for the project containing the `.cementover.yml` file.

__Possible values:__

* **_nil_**: take no action.

* __true__: disable this build step.

* __ShellCommand__: a shell command to execute.

* __ShellCommand[]__: an array of shell commands to execute.  All commands are executed in parallel.  If any command fails (exits with value > 0), all outstanding commands are allowed to complete.

* __Conditional[]__: an array of [Conditional](#conditional) objects.

__Default value:__ _nil_

__Examples:__

```yml
script: make build
```

```yml
script:
  - echo "Building $CEMENT_CURRENT_PROJECT_NAME"
  - make build
```

```yml
script:
  - do: make build
    on:
      changed: /src/**
  - do: make merge_config
    on:
      changed: /config/**
```

----------

## Common Structures

There are a number of common structures used through Cement configuration files.

----------

### Conditional

A set of configuration rules that dictate an action to perform when a given set of criteria are met.  This configuration structure is typically used in an array.

__Keys include:__

* `do`: _(required)_ a shell command to run.

* `on`: _(required)_ specifies the criteria that must be `true` in order to perform the action specified by `do`.  The value of `on` is an object that must include at least one of the following keys:

  + `changed`: _(optional)_ returns `true` if the given glob matches any file that has been modified as part of the push that triggered the build.

  + `condition`: _(optional)_ returns `true` if the given shell script exits with `0`.

  If more than one `on` clause is specified, then all clauses must return `true` for Cement to execute the action specified by `do`.  The order in which clauses are executed is not guaranteed.

__Example:__

```yml
script:
  - do: make build
    on:
      changed: /src/**
  - do: make merge_config
    on:
      changed: /config/**
      condition: make validate_config
```

----------

### Deployment

A set of instructions for how to deploy an application.  A `Deployment` object is an extension of Travis CI's native [deployment](https://docs.travis-ci.com/user/deployment/) instructions.  The difference is the addition of Cement's `changed` clause to a deployment configuration's `on` conditionals.  This `changed` conditional accepts a glob that instructs Cement to only trigger a the deployment logic if there is a match against changed files in the push that triggered the build.

__Example:__

```yml
deploy:
  provider: heroku
  api_key: ...
  app: $CEMENT_CURRENT_PROJECT_NAME
  on:
    branch: release
    changed: /src/**
```

----------

## Environment Variables

* `$CEMENT_CURRENT_PROJECT_NAME`: the name of the current project being worked on by Cement.

* `$CEMENT_CURRENT_STATUS`: the current build status.

* `$CEMENT_MODIFIED_PROJECTS`: a comma-delimited list of all project names that have been modified.
