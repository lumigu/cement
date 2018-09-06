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

## Base Configuration

The base configuration is stored in a `.cement.yml` file in the root of a monorepo.

----------

### `after_all`

----------

#### `after_all: after_deploy`

----------

#### `after_all: after_failure`

----------

#### `after_all: after_script`

----------

#### `after_all: after_success`

----------

#### `after_all: before_deploy`

----------

#### `after_all: before_install`

----------

#### `after_all: before_script`

----------

#### `after_all: deploy`

----------

#### `after_all: install`

----------

#### `after_all: rollback`

----------

#### `after_all: script`

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

### `rollback`

----------

### `script`

----------

### `override`

Dictates how 

__Possible values:__

* **_nil_**: use the default.

* __`merge`__: all build step configuration is merged with the base.

* __`replace`__: all build step configuration completely overwrites the base.

__Default value:__ `merge`

__Example:__

```yml
override: replace
```

----------

## Common Structures
