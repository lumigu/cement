# Cement for Travis CI

[![Build Status](https://secure.travis-ci.org/dsfields/travis-monorepo.svg)](https://travis-ci.org/dsfields/travis-monorepo)

## Overview

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

## Usage

[Documentation available here.](http(s)://dsfields.github.io/cement)

## Disclaimer

The `travis_monorepo` utility is an independent, open source project that is not affiliated with, nor supported by, Travis CI, GmbH.
