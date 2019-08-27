# Webpack

Webpack has two primary entrypoints:
* the configuration
* the CLI (which calls into the programmatic API)

We'll start with the CLI.

## `webpack` command

The `webpack` command installed by the base `webpack` package expects the `webpack-cli` command to be available, so you must have the `webpack-cli` package installed in order to run Webpack. Once that is done, the `webpack` command will forward all arguments to the `webpack-cli` command.

This package consists largely of one file and a few utility files.

It performs the following steps:
1. Prefer a local installation of `webpack-cli` to a global one (using the `import-local` package)
2. Install the `v8-compile-cache` to speed things up
3. Defer to a "non-compilation" command if one was provided (details below)
4. Configure a `yargs` CLI processor (details below)
5. Parse the arguments using this processor
6. Exit on a parse error
7. If the `help` or `version` options were passed, output the corresponding text and exit
8. Enable "verbose" display if the `verbose` option was provided
9. Convert the parsed arguments to options (print and exit on error)
10. If the `silent` argument was provided, swap out `process.stdout` with a noop version
11. Process each option one by one (details below)

### "Non-Compilation" Commands

Webpack comes with the following "non-compilation" commands:
* `init`
* `migrate`
* `serve`
* `generate-loader`
* `generate-plugin`
* `info`

Each of these commands defers to a separate package (`@webpack-cli/{command}`), and all are out of the scope of this exploration because they do not represent "core" Webpack logic.

### Option Configuration

Webpack CLI allows a large list of options (formatted directly from the source code, arranged in specified groups):

* Basic Group
    * `--context`: The base directory (absolute path!) for resolving the `entry` option. If `output.pathinfo` is set, the included pathinfo is shortened to this directory.
    * `--entry`: The entry point(s) of the compilation.
    * `--watch`: Enter watch mode, which rebuilds on file change.
    * `--debug`: Switch loaders to debug mode
    * `--devtool`: A developer tool to enhance debugging.
    * `--d`: shortcut for --debug --devtool eval-cheap-module-source-map --output-pathinfo
    * `--p`: shortcut for --optimize-minimize --define process.env.NODE_ENV="production"
    * `--progress`: Print compilation progress in percentage
* Config Group
    * `--config`: Path to the config file
    * `--config-register`: Preload one or more modules before loading the webpack configuration
    * `--config-name`: Name of the config to use
    * `--env`: Environment passed to the config, when it is a function
    * `--mode`: Enable production optimizations or development hints.
* Module Group
    * `--module-bind`: Bind an extension to a loader
    * `--module-bind-post`: Bind an extension to a post loader
    * `--module-bind-pre`: Bind an extension to a pre loader
* Output Group
    * `--output`: The output path and file for compilation assets
    * `--output-path`: The output directory as **absolute path** (required).
    * `--output-filename`: Specifies the name of each output file on disk. You must **not** specify an absolute path here! The `output.path` option determines the location on disk the files are written to, filename is used solely for naming the individual files.
    * `--output-chunk-filename`: The filename of non-entry chunks as relative path inside the `output.path` directory.
    * `--output-source-map-filename`: The filename of the SourceMaps for the JavaScript files. They are inside the `output.path` directory.
    * `--output-public-path`: The `publicPath` specifies the public URL address of the output files when referenced in a browser.
    * `--output-jsonp-function`: The JSONP function used by webpack for async loading of chunks.
    * `--output-pathinfo`: Include comments with information about the modules.
    * `--output-library`: Expose the exports of the entry point as library
    * `--output-library-target`: Type of library
* Resolve Group
    * `--resolve-alias`: Redirect module requests
    * `--resolve-extensions`: Extensions added to the request when trying to find the file
    * `--resolve-loader-alias`: Setup a loader alias for resolving
* Optimize Group
    * `--optimize-max-chunks`: Try to keep the chunk count below a limit
    * `--optimize-min-chunk-size`: Minimal size for the created chunk
    * `--optimize-minimize`: Enable minimizing the output. Uses optimization.minimizer.
* Advanced Group
    * `--records-input-path`: Store compiler state to a json file.
    * `--records-output-path`: Load compiler state from a json file.
    * `--records-path`: Store/Load compiler state from/to a json file. This will result in persistent ids of modules and chunks. An absolute path is expected. `recordsPath` is used for `recordsInputPath` and `recordsOutputPath` if they left undefined.
    * `--define`: Define any free var in the bundle
    * `--target`: Environment to build for
    * `--cache`: Cache generated modules and chunks to improve performance for multiple incremental builds.
    * `--watch-stdin`: Stop watching when stdin stream has ended
    * `--watch-aggregate-timeout`: Delay the rebuilt after the first change. Value is a time in ms.
    * `--watch-poll`: Enable polling mode for watching
    * `--hot`: Enables Hot Module Replacement
    * `--prefetch`: Prefetch this request (Example: --prefetch ./file.js)
    * `--provide`: Provide these modules as free vars in all modules (Example: --provide jQuery=jquery)
    * `--labeled-modules`: Enables labeled modules
    * `--plugin`: Load this plugin
    * `--bail`: Report the first error as a hard error instead of tolerating it.
    * `--profile`: Capture timing information for each module.
* Display Group
    * `--silent`: Prevent output from being displayed in stdout
    * `--json`: Prints the result as JSON.
    * `--color`: Force colors on the console
    * `--no-color`: Force no colors on the console
    * `--sort-modules-by`: Sorts the modules list by property in module
    * `--sort-chunks-by`: Sorts the chunks list by property in chunk
    * `--sort-assets-by`: Sorts the assets list by property in asset
    * `--hide-modules`: Hides info about modules
    * `--display-exclude`: Exclude modules in the output
    * `--display-modules`: Display even excluded modules in the output
    * `--display-max-modules`: Sets the maximum number of visible modules in output
    * `--display-chunks`: Display chunks in the output
    * `--display-entrypoints`: Display entry points in the output
    * `--display-origins`: Display origins of chunks in the output
    * `--display-cached`: Display also cached modules in the output
    * `--display-cached-assets`: Display also cached assets in the output
    * `--display-reasons`: Display reasons about module inclusion in the output
    * `--display-depth`: Display distance from entry point for each module
    * `--display-used-exports`: Display information about used exports in modules (Tree Shaking)
    * `--display-provided-exports`: Display information about exports provided from modules
    * `--display-optimization-bailout`: Display information about why optimization bailed out for modules
    * `--display-error-details`: Display details about errors
    * `--display`: Select display preset
    * `--verbose`: Show more details
    * `--info-verbosity`: Controls the output of lifecycle messaging e.g. Started watching files...
    * `--build-delimiter`: Display custom text after build output

Many of these options map directly to a webpack configuration. There are too many of them to detail here, so we will wait to cover those until the actual implementation.

### Option Conversion

Once the options are parsed, they are converted to an options object. This involves the following steps:
1. 
