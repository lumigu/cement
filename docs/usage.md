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

## Handling Deployment Failures



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
deploy:
  provider: s3
  access_key_id: "YOUR AWS ACCESS KEY"
  secret_access_key: "YOUR AWS SECRET KEY"
  bucket: "S3 Bucket"
```

In this example, we're telling Cement to deploy `project-a` to AWS S3 instead of a Heroku app as defined in `.cement.yml`.

## Conditional Build Logic
