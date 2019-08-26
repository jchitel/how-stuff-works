# Webpack

Webpack has two primary entrypoints:
* the configuration
* the CLI (which calls into the programmatic API)

We'll start with the CLI.

## `webpack` command

The `webpack` command installed by the base `webpack` package expects the `webpack-cli` command to be available, so you must have the `webpack-cli` package installed in order to run Webpack. Once that is done, the `webpack` command will forward all arguments to the `webpack-cli` command.


