# How Next.js Works

So, what does Next.js actually do?

To find this out, let's start at the typical entry point of Next.js: the CLI.

## The CLI

Next.js specifies a single binary called `next`, which points to `next/dist/bin/next`, which is a Node executable script. It'll be easier to understand what's going on by looking at the source, so we'll go to the source of this directory: `next/bin/next.ts`.

This file's responsibility is to determine which command was selected, and call to it.

There are four available commands: `dev`, `build`, `start`, and `export`. If no command is specified, `dev` is the default.

First, the following is verified:
* `react` and `react-dom` are installed as direct app dependencies and available for import
* The version of React supports `React.Suspense` (16.6+)

You can also specify the following flags on the base command:
* `--version`: if specified, this will print the version and exit
* `--help`: if specified with no command, this will print the help and exit
* `--inspect`: this is a legacy option. if specified, an error will be thrown with instructions on how to enable the expected behavior

Each command has a corresponding file under `next/cli/`: `next-dev.ts`, `next-build.ts`, `next-start.ts`, and `next-export.ts`.

When a command is specified, that file is imported and it corresponding executor function is called asynchronously.

If the `dev` command was selected, an additional synchronous step is taken to set up a file watcher on `next.config.js` in the app root directory. When changes are detected, a message is printed notifying the user that the dev server will need to be restarted due to a configuration change.

From here, we'll dive into each command, starting with `dev`.

## `next dev`

`next dev` calls into `next/cli/next-dev.ts`. This file, like the other four command files, exports a single function which takes a list of CLI arguments forwarded to it from the root binary.

Like the root, if `--help` is specified, instructions specific to this command are printed, and then the program exits.

The basic structure of the command is: `next dev <dir> -p <port> -H <hostname>`:
* `<dir>` is an optional argument specifying the directory to place the `.next/` directory, which contains the build output. If not specified, the working directory that the command was executed in is used.
* `<port>` is the port to run the development server on (if omitted, the port 3000 is used by default)
* `<hostname>` is the IP address or hostname to bind to locally (you'll usually omit this, which will default to localhost)

The specified `dir` must exist and have a `pages/` subdirectory inside it.

At this point, Next will know the local URL at which the app can be accessed, as well as the root of the app.

Next then sends a notification to the "build store" (located at `next/build/output/`) that the app URL is now available. This will be used to print messages to the user when the build is complete and the app can be opened. (see ***TODO*** for more information about the build store).

Now Next can start the server!

### Next Dev Server

A Next server is a standalone entity that can handle HTTP requests. It exposes a standard request handler to be passed into an actual Node HTTP server.

The `next dev` command calls into a utility function that sets up both a Next server and a Node HTTP server using the provided options.

First, the root `next()` function (the one that you get when you `require('next')`) is called with two options: the `dir` for source and output files, and a `dev` flag set to true. The inside of this function is pretty simple. If `dev` is true, it imports the class at `next/server/next-dev-server` and returns a new instance of it, passing along the options. If `dev` is false, it passes along the options to the function exported by the `next-server` module, which we'll get into when we cover the `start` command. Regardless, a server (called `app`) is returned.

Then, `app.getRequestHandler()` is called to get the handler to pass into Node's `http.createServer()`. We await a promise to start this server at the provided port and hostname, and then return the Next server. `next dev` waits for this server to return, then calls `app.prepare()`. If an error occurs at any point in this process, `next dev` will print the error and kill the process.

Now, let's dig into the `DevServer` class from `next/server/next-dev-server`.

This class extends `Server` from `next-server/server/next.server`. We'll be touching on that class as we see it. Here, we'll just talk about the properties and constructor.

`Server` Properties:
* `dir`: this is used as the base directory for the full application. it represents where source files come from, as well as configuration and build output
* `quiet`: this flag turns off error reporting for the server
* `nextConfig`: this is the exported value from your next.config.js, which contains the configuration for the application
* `distDir`: this is the directory where build output goes (by default, this is `{dir}/.next`)
* `publicDir`: this is the directory where you can place static files that will be exposed publicly by your app (always `{dir}/public`)
* `pagesManifest`: this is the file where the manifest of your app's pages is stored (`{distDir}/server/pages-manifest.json` for server, `{distDir}/serverless/pages-manifest.json` for serverless)
* `buildId`: a unique identifier for the build (this is overridden to always be `development` for the dev server)
* `renderOpts.poweredByHeader`: a flag for whether to include a `X-Powered-By` header with responses (default true)
* `renderOpts.ampBindInitData`: hard to say what this is for, it enables a bunch of alternative code paths throughout the render process, plus it's experimental and undocumented (default false)
* `renderOpts.staticMarkup`: a flag that specifies whether the pages should be rendered as static (react-agnostic) markup instead of dehydrated react HTML (default false)
* `renderOpts.buildId`: a copy of `buildId` that is passed into rendering
* `renderOpts.generateEtags`: a flag that specifies whether to generate an ETag header (default true)
* `renderOpts.runtimeConfig`: stores the runtime configuration exposed to the client via `publicRuntimeConfig` in the Next config file
* `renderOpts.assetPrefix`: applies a prefix for asset urls (not exactly sure what this means just yet)
* `renderOpts.canonicalBase`: something to do with AMP
* `renderOpts.documentMiddlewareEnabled`: a flag that specifies whether the renderer should use a provided `DocumentMiddleware` function (not sure where this comes from yet)
* `renderOpts.dev`: set by `DevServer` to enable some development-only workflows
* `compression`: if `compress` is set to true in the configuration, this will be a reference to a compression middleware
* `router`: the server-side router
* `dynamicRoutes`: a dictionary of the dynamic routes

The constructor takes four arguments:
* `dir`: the base directory of the project (defaults to the working directory)
* `staticMarkup`: the flag that makes pages render to static markup (defaults to false)
* `quiet`: flag that disables errors (defaults to false)
* `conf`: a custom configuration object, which overrides the configuration file (defaults to null)

The constructor effectively just initializes the list of variables above. We'll dive into a few of the more juicy bits that happen.

#### Configuration Loading

The configuration is loaded using the `loadConfig()` function (from `next-server/server/config`). This function takes:
* A `phase` string, which is only used when the user-specified configuration returns a function. The phase is passed as the first parameter to the configuration function, and there is one phase for each command: `phase-export`, `phase-production-build`, `phase-production-server`, and `phase-development-server`.
* `dir`, which is the same variable that we've seen before, the directory to look for a next.config.js file. However, `loadConfig()` will actually search up the directory hierarchy until it finds one.
* `customConfig`, which is an optional config object that will have defaults applied to it, overriding the normal config lookup.

This process starts by first checking if `customConfig` was provided. If so, it is passed into an `assignDefaults()` function that applies the custom config onto the default config, and the result is returned.

This is the default configuration:
* `env: []`
* `webpack: null`
* `webpackDevMiddleware: null`
* `distDir: '.next'`
* `assetPrefix: ''`
* `configOrigin: 'default'`
* `useFileSystemPublicRoutes: true`
* `generateBuildId: () => null`
* `generateEtags: true`
* `pageExtensions: ['tsx', 'ts', 'jsx', 'js']`
* `target: process.env.__NEXT_BUILDER_EXPERIMENTAL_TARGET || 'server'`
* `poweredByHeader: true`
* `compress: true`
* `onDemandEntries.maxInactiveAge: 60_000`
* `onDemandEntries.pagesBufferLength: 2`
* `amp.canonicalBase: ''`
* `exportTrailingSlash: false`
* `experimental.cpus: {number of cpus or 1}`
* `experimental.ampBindInitData: false`
* `experimental.terserLoader: false`
* `experimental.profiling: false`
* `experimental.flyingShuttle: false`
* `experimental.documentMiddleware: false`
* `experimental.granularChunks: false`
* `experimental.publicDirectory: false`
* `experimental.modern: false`
* `serverRuntimeConfig: {}`
* `publicRuntimeConfig: {}`

These items will be explained as we see how they are used.

If a custom config was not provided, then the loader searches for a next.config.js file starting at the provided directory, working its way up the hierarchy. If it doesn't find one, the default config is returned.

If a next.config.js is found, it is imported. It is then "normalized", which passes the phase and the default config into the config function, if it is one. The result is an actual configuration object.

The `target` is then validated, which must be one of: `server`, `serverless`, `experimental-serverless-trace`.

If `amp.canonicalBase` was provided, any trailing slash is removed.

An error is then thrown if the user specified both `target = 'serverless'` and a non-empty `publicRuntimeConfig`.

The defaults are then assigned, and the result is returned.





## TODO

* build store
* next-server