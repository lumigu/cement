# CLI Reference

Use the Cement command line interface to build and deploy your monorepos with Travis CI.

```sh
$ cement <command> [...<option>]
```

| Command   | Option | Description                                                                                                                                                          |
|-----------|--------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `build`   |        | Looks for a `.cement.yml` file in the current working directory to build your monorepo.  Returns __0__ if all build stages completed successfully.  Otherwise __1__. |
|           | `-b`   | Instructs Cement to only run build-related stages, and no deploy related stages (`before_deploy`, `deploy`, `after_deploy`, and `rollback`)                          |
| `help`    |        | Show all CLI options.                                                                                                                                                |
| `version` |        | Show the current version of `cement`.                                                                                                                                |
