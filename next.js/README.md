# Next.js

[Next.js](https://github.com/zeit/next.js) is a framework for building web applications using React. Specifically, the framework facilitates both the client and the server, meaning that you can run `next start` to simply spin up a working web server ready and hosting an application. Next's big feature is that it takes advantage of React's server-side rendering capabilities to create a fully isomorphic application. You write your components once, and Next renders them on both the server and the client.

Next is well-loved for its ease of use. It is dead simple to spin up a Next application, because it comes pre-bundled with literally everything you need to build and run a web application. The only thing you need to do yourself is install React, allowing you to control which version of React that you use (as long as it supports all of the React features that Next needs). From there, Next provides only four commands, which are all you need to develop, build, and deploy your app:
* `next dev` starts a development server complete with hot reloading so that you can focus on writing code.
* `next build` builds and packages your app for production deployment.
* `next start` runs a production server from the product of a `next build`.
* `next export` is a variant of `next build` that builds and packages your app as a static website instead of a dynamic server, good for environments where you don't control the server (such as GitHub pages).

With all of the features that Next provides baked in, what does the user actually need to do? The user writes "pages", which are React components organized in a "pages" directory at the root of the project. The organization of these files in the file system directly maps to the routes of these pages in the application. A page located at "~/pages/my-app/about" will render when you navigate to "/my-app/about" on your site. The only variant from regular React development is that the root component exported from a "page" file is a Next page, not a vanilla React component. A Next page contains additional logic allowing you to fetch the initial props provided to it, in a way that will work on both the client and the server. This page is also mounted into a fully Next-controlled document rendered on the server. This document's content can be overridden in an app-wide custom file.

Next.js is extremely powerful, and a great step forward in the evolution of web development. However, I believe that it falls short in a few ways. Being a framework, it provides a lot for the user, much of which is customizable. However, in my mind, it's a bit too "framework-y". Diverging from the default use case is often difficult, and the framework opts you in to a structure that isn't always ideal. For example, I don't much like the file system routing. I personally believe that it should be the responsibility of the code to structure the application, and the user should be free to specify that structure however they please, as long as the API supports it. It also isn't particularly extensible. There are "middleware-like" libraries available, but they only allow you to control the configuration. For example, I can't swap out webpack for a different bundler because Next is too tightly coupled to it. I also can't customize the server that Next uses internally.

I believe that a plugin-based approach is best for a framework like Next. Much like Babel and now Parcel, both of which started as monolithic tools and shifted toward a plugin approach, a framework like this should provide a core engine and a set of built-in plugins, all of which can be swapped out for third party alternates.

At its absolute core, Next is a web server, a code builder, and a layer on top of React. From there, everything that it provides can be thought of as a middleware or a plugin for those technologies:
* Server-side rendering
* Built-in Webpack
* CSS-in-JS
* File system routing