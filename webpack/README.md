# Webpack

Webpack is a tool for building web and JavaScript applications and libraries. Its introduction represented a key step in the evolution of modern web development, and came with a major shift in how JS applications are developed.

Webpack arose from the natural desire to develop JS applications and libraries the same way as other platforms. JS presents a complicated situation because there is no other platform that works like the web.

Effectively, what Webpack does is to take a modularized codebase with external dependencies and package it in a way that is still performant and easily distributable for the web. The platform provided by Webpack inherently comes with a bunch of other secondary goodies, such as transpiling code and loading non-JS dependencies.

The external API provided by Webpack is its CLI (backed by a programmatic API) and its configuration. In order to operate, Webpack requires:
* one or more entrypoints
* a set of rules for how to load files
* an output configuration

There are many more other optional configurations available, but these are the necessary ones. Technically, these days, even this list isn't necessary, as Webpack comes pre-defined with sensible defaults for entries, loaders, and outputs. However, for any practical application, you typically still need at least these three things.
