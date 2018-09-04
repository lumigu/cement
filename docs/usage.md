# Usage

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
    app: $CEMENT_PROJECT_NAME
```

In this example, for each project that has changed, we are:

1. Installing the project's dependencies.
2. Running tests and build logic.
3. Deploying the project to Heroku.  Notice that the `app` name is set to an environment variable called `$CEMENT_PROJECT_NAME`, which his set to the directory name of the project currently being deployed.

In the monorepo project described above if, `project-a` and `project-c` both had changes, then the operations described by `install`, `script`, and `deploy` would be run from within the `project-a` and `project-c` directories.
