# Overview

Cement is a small utility for building and deploying projects managed in a [monorepo](https://cacm.acm.org/magazines/2016/7/204032-why-google-stores-billions-of-lines-of-code-in-a-single-repository/fulltext) with [Travis CI](https://travis-ci.com/).

The challenge with monorepos is in ensuring the build pipeline only runs build and deploy logic on the projects that have changed, and further distinguishing between when to build and when to deploy depending on the type of change.  For example, you would not want to run build logic if you've only made a configuration change.

Cement helps you address these challenges while utilizing the full power of Travis CI.

## Requirements

* Python 2.7
* Linux, Windows, macOS

## Installation

Installing Cement is quick and easy with `pip`:

```sh
$ pip install travis_cement
```

## Benefits

Monorepos offer a number of benefits:

* There is less to administer in GitHub.

* It's easier to work locally with a single repository rather than keeping dozens, or even hundreds, of repositories up to date.

* Build configuration is consolidated; which, cuts down on "copy and paste" of similar configuration across many repositories, and makes it easier to role out changes to the build pipeline.

* A large application made up of a many repositories can be versioned together.

* Feature development that spans multiple projects can be managed and reviewed in a single pull request.

## Disclaimer

Cement is an independent, open source project that is not affiliated with, nor supported by, Travis CI, GmbH.
