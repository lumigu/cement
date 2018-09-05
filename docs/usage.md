# Usage

* [Getting Started](#getting-started)
* [Monorepo Structure](#monorepo-structure)
* [Configuring Cement](#configuring-cement)
* [Global Build Stages](#globl-build-stages)
* [Handling Deployment Failures](#handling-deployment-failures)
* [Override Root Configuration](#overriding-root-configuration)
* [Conditional Build Logic](#conditional-build-logic)

## Getting Started

To get started using Cement simply add the following to the `.travis.yml` file found in the root of your GitHub repository.

```yml
install: pip install travis_cement
script: cement build
```

You can further define your Travis CI [cache](https://docs.travis-ci.com/user/caching/), addons, and [build matrix](https://docs.travis-ci.com/user/customizing-the-build/#build-matrix) as you normally would.  However, additional build logic should generally be managed within a `.cement.yml` file.  This is to ensure Cement can manage what logic is executed based on what projects have actually changed.

## Monorepo Structure

The basic structure of a monorepo is very simple.

```
📁 my-monorepo
    📁 project-a
    📁 project-b
    📁 project-c
    📄 .cement.yml
    📄 .travis.yml
```

Here, we have three projects managed within a monorepo: `project-a`, `project-b`, `project-c`.  Normally, these projects would be separate repositories in Git with their own build pipeline and Travis CI configuration.  Cement allows us to build all of these projects in a single pipeline, and minimize redundant build configuration.

Each directory in a monorepo is treated as a project, and Cement will only execute build and deploy logic on project directories that have been changed as part of a commit to the repository.

## Configuring Cement

In the root of your repository create a `.cement.yml` to define your build and deploy logic.

```yml
for_each:
  install: make install
  script: make test && make build
  deploy:
    provider: heroku
    api_key: "YOUR API KEY"
    app: $CEMENT_CURRENT_PROJECT_NAME
```

In this example, for each project that has changed, we are:

1. Installing the project's dependencies.

2. Running tests and build logic.

3. Deploying the project to Heroku.  Notice that the `app` name is set to an environment variable called `$CEMENT_CURRENT_PROJECT_NAME`, which is set to the directory name of the project currently being built or deployed.

In the monorepo project described above if, `project-a` and `project-c` both had changes, then the operations described by `install`, `script`, and `deploy` would be run from within the `project-a` and `project-c` directories.

## Global Build Stages

In some cases, you may have additional build logic you want to run before or after a specific stage for all projects with changes have run.  This is where `before_all` and `after_all` come in.  In your `.cement.yml` file:

```yml
before_all:
  script: echo "STARTING BUILD SCRIPT FOR PROJECTS $CEMENT_MODIFIED"

after_all:
  after_failure: curl -d "build_num=$TRAVIS_BUILD_NUMBER&build=$TRAVIS_REPO_SLUG&status=failed" -H "Authorization: Basic abc123" -X POST https://api.my-build-badges.com/notifications
```

Here we're telling Cement to:

1. Before the `script` stage runs for any project that has been modified echo out a message including a list of all projects have been modified (available in the environment variable `$CEMENT_MODIFIED`).

2. After the `script` stage has completed for all modified projects, and if any of them had a failure, curl a badge system API.

## Overriding Root Configuration

The root `.cement.yml` file is designed to allow you to define build logic for every project in the monorepo.  However, there will be instances where this logic will need to be overridden and tailored to specific projects.  This can be accomplished by creating `.cementover.yml` files in projects.

```
📁 my-monorepo
    📁 project-a
        📄 .cementover.yml
    📁 project-b
    📁 project-c
    📄 .cement.yml
    📄 .travis.yml
```

The contents of this file contains overrides for the build stages defined in the `.cement.yml` file's `for_each`:

```yml
install: true
script: true
deploy:
  provider: s3
  access_key_id: "YOUR AWS ACCESS KEY"
  secret_access_key: "YOUR AWS SECRET KEY"
  bucket: "my-bucket"
```

In this example, we're telling Cement to deploy `project-a` to AWS S3 instead of a Heroku app as defined in `.cement.yml`.  Since this project holds static assets that do not require building, we're disable both `install` and `script`.

## Handling Deployment Failures

When there are changes to multiple projects that result in more than one deployment, all deployments are run serially.  In this scenario it's possible that a deployment fails after one or more deployments have succeeded.  This can put your application in an inconsistent state.  In which case, you will want to rollback changes you had previously deployed.

To accommodate this scenario Cement introduces a new build stage called `rollback`.  This stage is configured just like all other build stages.  It is invoked for every project that has deployed prior to a failed deploy.

```yml
for_each:
  rollback: heroku rollback
```

In this example, we are utilizing the [Heroku CLI](https://devcenter.heroku.com/articles/heroku-cli) to [rollback](https://devcenter.heroku.com/articles/releases) a release performed with the Heroku deployment provider.

Rollback logic can also be used in `.cementover.yml` files.

```yml
rollback: aws s3 ls s3://my-bucket| awk '{print $4}' | xargs -L 1 aws s3api restore-object --restore-request Days=1 --bucket my-bucket --key
```

In this case, we're overriding rollback logic for the `.cementoverride.yml` file we previously created for `project-a`.

## Conditional Build Logic

In some situations you may want to execute different build logic based on the kinds of changes that have been made.  Expanding on our example above:

```
📁 my-monorepo
    📁 project-a
        📄 .cementover.yml
    📁 project-b
        📁 config
        📁 src
    📁 project-c
        📁 config
        📁 src
    📄 .cement.yml
    📄 .travis.yml
```

In this case, we're managing configuration and source code within each project.  In such a scenario we'll likely want to handle build logic different if there is a change to `config` vs if there is a change to `src`.  For example, we likely do not need to do a full build if all we've done is changed configuration.

To handle this, we can use conditions to dictate what build logic is run based on what changed.  The concept is not too dissimilar from Travis CI's [`deploy on` conditions](https://docs.travis-ci.com/user/deployment/#conditional-releases-with-on).

```yml
for_each:
  script:
    - do: make build
      on:
        changed: ^\/src
    - do: make merge_config
      on:
        changed: ^\/config
```

Here we are defining an array of objects for `script`, where each object has to properties: `do` and `on`.  A `do` property defines the steps for the `script` stage.  An `on` property defines the conditions that specify when the steps defined by `do` should run.

In this example, we're telling Cement to only run `make build` when changes are detected on anything in a `src` directory.  When there are changes to files in `config` configuration merge logic is run.

Note that changed files paths that are evaluated with the regular expression in `on changed` are relative to the root of the project directory.

This schema for defining conditional logic can be used with all build steps, including `deploy`.  The `do` property is always the value that you would normally set for the build stage.
