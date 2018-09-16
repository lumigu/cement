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
  + [`install`](#install)
  + [`override`](#override)
  + [`rollback`](#rollback)
  + [`script`](#script)
* [Common Structures](#common-structures)
  + [Conditional](#conditional)
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
    - do: echo "Application build completed with $CEMENT_STATUS."
      on:
        changed: /*/src/**
    - do: echo "Configuration merge complete with $CEMENT_STATUS."
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
    - sh finalizeDeploy.sh
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

---------

#### `before_all: after_deploy`

----------

#### `before_all: after_failure`

----------

#### `before_all: after_script`

----------

#### `before_all: after_success`

----------

#### `before_all: before_deploy`

----------

#### `before_all: before_install`

----------

#### `before_all: before_script`

----------

#### `before_all: deploy`

----------

#### `before_all: install`

----------

#### `before_all: rollback`

----------

#### `before_all: script`

----------

### `for_each`

---------

#### `for_each: after_deploy`

----------

#### `for_each: after_failure`

----------

#### `for_each: after_script`

----------

#### `for_each: after_success`

----------

#### `for_each: before_deploy`

----------

#### `for_each: before_install`

----------

#### `for_each: before_script`

----------

#### `for_each: deploy`

----------

#### `for_each: install`

----------

#### `for_each: rollback`

----------

#### `for_each: script`

----------

### `ignore`

Defines one or more globs used to identify directories and files Cement should ignore.  This property is when Cement is figuring which projects on which to execute build and deploy logic.  All globs are compared against the full paths of all modified files in the Git commit that triggered the build.  Matches are ignored.

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

----------

### `after_failure`

----------

### `after_script`

----------

### `after_success`

----------

### `before_deploy`

----------

### `before_install`

----------

### `before_script`

----------

### `deploy`

----------

### `install`

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

----------

### `script`

----------

## Common Structures

### Conditional


## Environment Variables
